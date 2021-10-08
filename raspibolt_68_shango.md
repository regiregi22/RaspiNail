---
layout: default
title: Shango Mobile
parent: Bonus Section
nav_order: 70
has_toc: false
---
## Bonus guide: Shango Mobile Lightning Wallet

*Difficulty: medium*

The mobile app Shango (http://shangoapp.com) allows the managment of the essential features of LND on your RaspiNail. It provides an status overview, lists peers, allows to open & close channels, and of course you can make payments and create invoices.

At the moment this app is in beta testing. If you find bugs, you can contribute to this project by reporting them here: https://github.com/neogeno/shango-lightning-wallet/issues.

![Shango app overview](images/60_shango.png)

### Preparation on the Pi

This guide describes how to use Shango from within your own network, the same that also connects your RaspiNail. It is perfectly possible to use Shango on-the-go and connect to your node at home, but this involves creating new TLS certificates and can conflict with other parts of this guide.

* Add the following line to your lnd configuration file in the section `[Application Options]`  

  `$ sudo nano /home/bitcoin/.lnd/lnd.conf`

   ```bash
   rpclisten=0.0.0.0:10009
   tlsextraip=192.168.0.0/24  #<-- change this to fit your ip mask, if might bit 192.168.1.0/24
   ```

* Open port 10009 so that Shango wallet can talk to your Lightning node, but only from within the local network (The line ```sudo ufw allow from 192.168.0.0/24…``` below assumes that the IP address of your Pi is something like ```192.168.0.???```, the ??? being any number from 0 to 255. If, for example, your IP address is ```12.34.56.78```, you must adapt this line to ```sudo ufw allow from 12.34.56.0/24…```., see [more details](raspibolt_20_pi.md#hardening-your-pi))  

  ```bash
  $ sudo ufw allow from 192.168.0.0/24 to any port 10009 comment 'allow LND grpc from local LAN'
  $ sudo ufw enable
  $ sudo ufw status
  ```

* Restart LND and unlock wallet  

  ```bash
  $ sudo systemctl restart lnd
  $ lncli unlock
  ```

* Install QR encoder to pass our super-secret admin information to the app  

  ```bash
  $ sudo apt install qrencode
  $ cd /home/admin/.lnd
  ```
  
* Set the Bitcoin network:
  * on testnet: `$ export NETWORK=testnet`
  * on mainnet: `$ export NETWORK=mainnet` 

### Configure Shango app

* Start app & go to "Settings" / "Connect to other LND Servers"
  
* On your RaspiNail, enter the following command and "Scan QR" with the app

  ```bash
  echo -e "$(curl -s ipinfo.io/ip),\n$(xxd -p -c2000 ~/.lnd/data/chain/bitcoin/$NETWORK/admin.macaroon),\n$(openssl x509 -sha256 -fingerprint -in ~/.lnd/tls.cert -noout)" > qr.txt && qrencode -t ANSIUTF8 < qr.txt
  ```

* In the app, the input field "IP:Port" is filled with your external ip address. Make sure to replace it with your internal ip address (eg. `192.168.0.20:10009`).  
* Click on "Connect" and the app should sync with your RaspiNail.

---

<< Back: [Bonus guides](raspibolt_60_bonus.md) 
