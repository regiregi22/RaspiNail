---
layout: default
title: System overview
parent: Bonus Section
nav_order: 10
has_toc: false
---
## Bonus guide: System overview

*Difficulty: easy*

To get a quick overview over the system status, @Tx0k0 has created [a shell script](resources/20-raspinail-welcome) that is run as "message of the day" (motd) to be shown on login or on demand.

![MotD system overview](images/60_status_overview.png)

This script will run as root, so please check it before blindly trusting me.

```sh
$ sudo apt install bc jq net-tools
$ cd /tmp/
$ wget https://raw.githubusercontent.com/regiregi22/raspinail/master/resources/20-raspinail-welcome

# check script & exit
$ nano 20-raspinail-welcome

# delete existing welcome scripts and install
$ sudo mv /etc/update-motd.d /etc/update-motd.d.bak
$ sudo mkdir /etc/update-motd.d
$ sudo cp 20-raspinail-welcome /etc/update-motd.d/
$ sudo chmod +x /etc/update-motd.d/20-raspinail-welcome
$ sudo ln -s /etc/update-motd.d/20-raspinail-welcome /usr/local/bin/raspinail
```

In case the script runs into problems, it could theoretically prevent you from logging in. We therefore disable all motd execution for the "root" user, so you will always be able to login as "root" to disable it.

```sh
$ sudo su
$ touch /root/.hushlogin
$ exit
```

You can now start the script with `sudo raspinail` and it is shown every time you log in.

If the script is showing 'Public Not reachable' but you do have incoming connections and the blockchain is syncing, you might have a router that does not support NAT Loopback. Please check your node at https://bitnodes.earn.com, if it displays your node as available remove the # on lines 147 and 148 and put them before 149 and 150 by editing the file:
```sh
$ sudo nano /etc/update-motd.d/20-RaspiNail-welcome
```
Both methods work, but the original method does not rely on third party applications and the earn.com method obviously does, but it is better than no working method at all.

------

<< Back: [Bonus guides](raspibolt_60_bonus.md)
