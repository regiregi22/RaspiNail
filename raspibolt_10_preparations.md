---
layout: default
title: Preparations
nav_order: 10
---
<!-- markdownlint-disable MD014 MD022 MD025 MD040 -->
# Preparations
{: .no_toc }

Let's get all the required parts and assemble the Raspberry Pi.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

This guide builds on the easily available and very flexible Raspberry Pi 4.
This amazing piece of hardware is a tiny computer-on-a-chip, costs about $60 and consumes very little energy.

## Hardware requirements

This guide is written for the fastest Raspberry Pi, as it makes a lot of hacks and workarounds obsolete.
You need

* Raspberry Pi 4, with 4 GB RAM (8 GB recommended for future-proof)
* microSD card: 8 GB or more, incl. adapter to your regular computer
* Official RPi USB power adapter: 5V/3A + USB-C cable
* External SSD: 1 TB or more (2 TB recommended for future-proof)
* optional: Raspberry Pi case

![Raspberry Pi](images/10_raspberrypi_hardware.png)

To run a Lightning node, the full Bitcoin blockchain must be stored locally, which is over 300 GB and growing.
You can buy a cheap hard disk enclosure and reuse an old 1 TB SSD or larger. If you also plan to run Electrs and the Block Explorer, you should use a 1TB disk or larger.
I recommend getting a modern 2.5" SSD that can be powered through the USB connection to the Pi directly, which also speeds up initial sync time significantly.

A recommended setup could be something like:

* Raspberry Pi 4 8GB (https://www.amazon.es/gp/product/B0899VXM8F/)
* Official RPi power adapter (https://www.amazon.es/gp/product/B07TMPC9FG/)
* Raspberry case (https://www.amazon.es/gp/product/B08HMRPXWT/)
* SanDisk MAX Endurance 32GB (https://www.amazon.es/gp/product/B084CJLNM4)
* Samsung SSD 870 EVO 1TB (https://www.amazon.es/gp/product/B08PC5DKZQ)
* UGREEN SATA-USB Case (https://www.amazon.es/gp/product/B06XWSDGP6/)

---

## Write down your passwords

You will need several passwords and I find it easiest to write them all down in the beginning, instead of bumping into them throughout the guide.
They should be unique and very secure, at least 12 characters in length. Do **not use uncommon special characters**, spaces or quotes (‘ or “).

```console
[ A ] Master user password
[ B ] Bitcoin RPC password
[ C ] LND wallet password
[ D ] LND seed password (optional)
```

![xkcd: Password Strength](images/20_xkcd_password_strength.png)

If you need inspiration for creating your passwords: the [xkcd: Password Strength](https://xkcd.com/936/) comic is funny and contains a lot of truth.
Store a copy of your passwords somewhere safe (preferably in an open source password manager like [KeePassXC](https://keepassxc.org/)) and keep your original notes out of sight once your system is up and running.

---
Next: [Raspberry Pi >>](raspibolt_20_pi.md)
