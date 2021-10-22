---
layout: default
title: Privacy
nav_order: 22
---
<!-- markdownlint-disable MD014 MD022 MD025 MD033 MD040 -->
# Privacy
{: .no_toc }

We configure Tor to run your node anonymously.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

Running your own Bitcoin and Lightning node at home makes you a direct, sovereign peer on the Bitcoin network.
If not configured without privacy in mind, it also tells the world that there is someone with Bitcoin at that address.
True, it's only your IP address that is visible to others, but using services like [iplocation.net](https://www.iplocation.net){:target="_blank"}, your physical address can be determined quite accurately.

Especially with Lightning, your IP address is widely used, so we need to make sure that you keep your privacy.

---

## Tor Project

We will use Tor, a free software built by the [Tor Project](https://www.torproject.org){:target="_blank"}, that allows you to anonymize internet traffic by routing it through a network of nodes, hiding your location and usage profile.

It is called "Tor" for "The Onion Router": information is encrypted multiple times with the public keys of the nodes it passes through. Each node decrypts the layer of information that corresponds to its own private key, knowing only the last and next hop of the route, like peeling an onion, until the data reaches its destination.

---

## Installing Tor

Log in your RaspiNail via SSH as user "admin".

* Add the following two lines to `sources.list` to add the torproject repository.

  ```sh
  $ sudo nano /etc/apt/sources.list
  ```

  ```
  deb https://deb.torproject.org/torproject.org impish main
  deb-src https://deb.torproject.org/torproject.org impish main
  ```

* In order to verify the integrity of the Tor files, download and add the signing keys of the torproject.

  ```sh
  $ sudo apt update
  $ sudo apt install dirmngr apt-transport-https
  $ curl https://deb.torproject.org/torproject.org/A3C4F0F979CAA22CDBA8F512EE8CBC9E886DDD89.asc | gpg --import
  $ gpg --export A3C4F0F979CAA22CDBA8F512EE8CBC9E886DDD89 | sudo apt-key add -
  ```

* The latest version of Tor can now be installed.

  ```sh
  $ sudo apt update
  $ sudo apt install tor
  ```

* Check the version of Tor (it should be 0.3.3.6 or newer) and that the service is up and running.

  ```sh
  $ tor --version
  > Tor version 0.4.5.10.
  $ systemctl status tor
  ```

* Check that within the "tor-service-defaults-torrc" file the "User" name is "debian-tor".

  ```sh
  $ cat /usr/share/tor/tor-service-defaults-torrc
  > User debian-tor
  ```

* Make sure that user "bitcoin" belongs to the "debian-tor" group.

  ```sh
  $ sudo adduser bitcoin debian-tor
  $ cat /etc/group | grep debian-tor
  > debian-tor:x:123:bitcoin
  ```

* Modify the Tor configuration by uncommenting (removing the #) or adding the following lines. On top of that, it will allow you to connect to your node from a TOR connection to the SSH port for remote management:

  ```sh
  $ sudo nano /etc/tor/torrc
  ```

  ```conf
  # uncomment:
  ControlPort 9051
  CookieAuthentication 1

  # add:
  CookieAuthFileGroupReadable 1
  
  HiddenServiceDir /var/lib/tor/hidden_service_ssh/
  HiddenServiceVersion 3
  HiddenServicePort 22 127.0.0.1:22
  ```

* Restart Tor to activate modifications

  ```sh
  $ sudo systemctl restart tor
  ```
  
* Take not of your TOR onion address for remote management:
  ```sh
$ sudo cat /var/lib/tor/hidden_service_ssh/hostname
> xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.onion
  ```
  
<script id="asciicast-xeGJH0YDOVZV719yn5rDL9GuP" src="https://asciinema.org/a/xeGJH0YDOVZV719yn5rDL9GuP.js" async></script>

Not all network traffic is routed over the Tor network.
But we now have the base to configure sensitive applications to use it.

---

Next: [Bitcoin >>](raspibolt_30_bitcoin.md)
