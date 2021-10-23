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

This guide builds on the easily available and very flexible Raspberry Pi 4. <br />
This amazing piece of hardware is a tiny computer-on-a-chip, costs about $60 and consumes very little energy.

## Hardware requirements

This guide is written for the fastest Raspberry Pi 4, as it makes a lot of hacks and workarounds obsolete.
A recommended setup could be something like:

* [Raspberry Pi 4 with 4 GB RAM (8 GB recommended for future-proof)](https://www.amazon.es/gp/product/B07TC2BK1X/)
* [Raspberry Pi enclosure](https://www.amazon.es/gp/product/B08HMRPXWT/)
* [Official RPi power adapter 5V/3A](https://www.amazon.es/gp/product/B07TMPC9FG/)
* [MicroSD card (min. 16Gb, recommended 32Gb)](https://www.amazon.es/gp/product/B084CJLNM4)
* [MicroSD USB reader](https://www.amazon.es/gp/product/B07L9VT8YY/)
* [SSD unit: 1 Tb (2 Tb recommended for future-proof)](https://www.amazon.es/gp/product/B08PC5DKZQ)
* [SSD enclosure](https://www.amazon.es/gp/product/B06XWSDGP6/)
* 

![Raspberry Pi](images/10_raspberrypi_hardware.png)

To run a Lightning node, the full Bitcoin blockchain must be stored locally, which is near 400 GB and growing daily.
I recommend getting a modern 2.5" SSD that can be powered through the USB connection to the Pi directly, which also speeds up initial sync time significantly.

---

## Write down your passwords

You will need several passwords and I find it easiest to write them all down in the beginning, instead of bumping into them throughout the guide.
They should be unique and very secure, at least 12 characters in length. Do **not use uncommon special characters**, spaces or quotes (‘ or “). <br />

You could use the [random.org](https://www.random.org/strings/) string generator, which is a good source of randomness. <br />
Or you can roll some dices and concatenate some random words for each password using the list from [EFF's New Wordlists for Random Passphrases](https://www.eff.org/files/2016/07/18/eff_large_wordlist.txt)

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
