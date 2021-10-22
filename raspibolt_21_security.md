---
layout: default
title: Security
nav_order: 21
---
<!-- markdownlint-disable MD014 MD022 MD025 MD033 MD040 -->
# Security
{: .no_toc }

We make sure that your RaspiNail is secure and reliable.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

The RaspiNail will be visible from the internet and therefore needs to be secured against attacks using various methods.

---

## Enabling the Uncomplicated Firewall

A firewall controls what traffic is permitted and closes possible security holes.
Only SSH and the Electrum server are reachable from the outside.
Bitcoin Core and LND are using Tor and don't need incoming ports.

The following steps need admin privileges and must be executed with the user "admin".

```sh
$ sudo apt install ufw
$ sudo su
$ ufw default deny incoming
$ ufw default allow outgoing
$ ufw allow 22    comment 'allow SSH'
$ ufw allow 50002 comment 'allow Electrum SSL'
$ ufw enable
  y
$ systemctl enable ufw
$ ufw status
> Status: active
>
> To                         Action      From
> --                         ------      ----
> 22                         ALLOW       Anywhere                   # allow SSH
> 50002                      ALLOW       Anywhere                   # allow Electrum SSL
> 22 (v6)                    ALLOW       Anywhere (v6)              # allow SSH
> 50002 (v6)                 ALLOW       Anywhere (v6)              # allow Electrum SSL
> ...
$ exit
```

<script id="asciicast-vRmJZZMgzolgH3ooLCGS5RCj8" src="https://asciinema.org/a/vRmJZZMgzolgH3ooLCGS5RCj8.js" async></script>

🔍 *more: [UFW Essentials](https://www.digitalocean.com/community/tutorials/ufw-essentials-common-firewall-rules-and-commands){:target="_blank"}*

💡 If you find yourself locked out by mistake, you can connect keyboard and screen to your Pi to log in locally and fix these settings (especially for the SSH port 22).

---

## fail2ban

The SSH login to the Pi must be especially protected.
Additional steps should be taken to prevent an attacker to just try out all possible passwords.

The first measure is to install “fail2ban”, a service that cuts off any system with five failed login attempts for ten minutes.
This makes a brute-force attack unfeasible, as it would simply take too long.

![fail2ban](images/20_fail2ban.png){:target="_blank"}
*Me locking myself out by entering wrong passwords*

```sh
$ sudo apt install fail2ban
```

The initial configuration should be fine as it is enabled for SSH by default.

<script id="asciicast-013bxZ8R7LktqzhP6O27LrorA" src="https://asciinema.org/a/013bxZ8R7LktqzhP6O27LrorA.js" async></script>

🔍 *more: [customize fail2ban configuration](https://linode.com/docs/security/using-fail2ban-for-security/){:target="_blank"}*

---

## Increase your open files limit

In case your RaspiNail is swamped with internet requests (honest or malicious due to a DDoS attack), you will quickly encounter the `can't accept connection: too many open files` error.
This is due to a limit on open files (representing individual tcp connections) that is set too low.

Edit the following three files, add the additional line(s) right before the end comment, save and exit.

```sh
$ sudo nano /etc/security/limits.conf
*    soft nofile 128000
*    hard nofile 128000
root soft nofile 128000
root hard nofile 128000
```

```sh
$ sudo nano /etc/pam.d/common-session
session required pam_limits.so
```

```sh
$ sudo nano /etc/pam.d/common-session-noninteractive
session required pam_limits.so
```
Reboot the system:
```sh
$ sudo reboot
```

<script id="asciicast-ZWxK6wLjrRs1AAnEJpXfIoyPb" src="https://asciinema.org/a/ZWxK6wLjrRs1AAnEJpXfIoyPb.js" async></script>

---

## rfkill - Disable Bluetooh

As we are not using Bluetooth on the node, it is more secure to just disable it:

```sh
$ sudo rfkill block 1
$ rfkill
ID TYPE      DEVICE      SOFT      HARD
 0 wlan      phy0   unblocked unblocked
 1 bluetooth hci0     blocked unblocked
```

Next: [Privacy >>](raspibolt_22_privacy.md)
