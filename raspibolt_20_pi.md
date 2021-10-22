---
layout: default
title: Raspberry Pi
nav_order: 20
---
<!-- markdownlint-disable MD014 MD022 MD025 MD033 MD040 -->

# Raspberry Pi
{: .no_toc }

We configure the Raspberry Pi and install the Linux operating system.

---

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## Preparing the operating system

The node runs headless, that means without keyboard or display, so the operating system Ubuntu Server is used.

1. Download and install the [Raspberry Pi Imager](https://www.raspberrypi.org/software/){:target="_blank"}
2. Choose "Other general-purpose OS" and then "Ubuntu Server 21.10 64-bit", choose your SD-card and write the image.

### Prepare Wifi

You can run your RaspiNail over Wifi.
To avoid using a network cable for the initial setup, you can pre-configure the wireless settings:

* Locate the file `network-config` in the boot partition of the microSD card. An example is already included in the file, you can simply adapt it.  
* If the boot partition doesn't appear in Windows, just open Start/"Create and format hard disk partitions", right click on the system-boot partition, and assing it a Drive Letter that is not already in use.

To do so, uncomment (remove the “#” at the beginning) and edit the following lines:

  ```
  wifis:
    wlan0:
      dhcp4: true
      optional: true
      access-points:
        <wifi network name>:
          password: "<wifi password>"
  ```
  
For example:

  ```
  wifis:
    wlan0:
      dhcp4: true
      optional: true
      access-points:
        "home network":
          password: "123456789"
  ```

* Note: network name must be enclosed in quotation marks.
* Save the file.
* Note ⓘ: During the first boot, your Raspberry Pi will try to connect to this network. It will fail the first time around. Simply reboot sudo reboot and it will work.

### Set a static IP

To set a static IP you need to replace the
  ```
  dhcp4: true
  ```
line in the network-config file with lines that specify the intended IP address as well as its default gateway and DNS server. You can do this for either the eth0 or wlan0 interface (or both). **It is important that you get the indenting right for this work correctly**

For example, if you were planning to give the pi the address 192.168.1.23 in the 192.168.1.0/24 subnet with a default gateway of 192.168.1.1 and a DNS server of 192.168.1.53 then the following text would work. The same structure works for both the eth0 or wlan0 sections in the file:
  ```
ethernets:
  eth0:
    addresses:
      - 192.168.101.23/24
    gateway4: 192.168.1.1
    nameservers:
      addresses: [192.168.1.23]
    optional: true
  ```


### Start your Pi

* Safely eject the sd card from your computer
* Insert the sd card into the Pi
* If you did not already setup Wifi: connect the Pi to your network with an ethernet cable
* Start the Pi by connecting it to the power adapter using the USB-C cable

---

## Connecting to the Raspberry Pi

### Find it

The Pi is starting and gets a new address from your home network.
Finding it can be a bit tricky without a screen.

* On your regular computer, open the Terminal (also known as "command line").
  Here are a few links with additional details for [Windows](https://www.computerhope.com/issues/chusedos.htm){:target="_blank"}, [MacOS](https://macpaw.com/how-to/use-terminal-on-mac){:target="_blank"} and [Linux](https://www.howtogeek.com/140679/beginner-geek-how-to-start-using-the-linux-terminal/){:target="_blank"}.
  
* To determine the IP address of your board, open a terminal and run the arp command:

**On Ubuntu and Mac OS:**
  ```
arp -na | grep -i "b8:27:eb"
  ```
If this doesn’t work and you are using the latest Raspberry Pi 4, instead run:
  ```
arp -na | grep -i "dc:a6:32"
  ```
If this doesn’t work and you are using the latest Raspberry Pi 4, instead run:
  ```
arp -na | grep -i "e4:5f:01"
  ```
  ```
Information
Depending on your version of Ubuntu, you may need to install the net-tools package. Install it with sudo apt install net-tools and try the arp command again.
  ```
  This will return an output similar to:
  ```
 ? (xx.xx.xx.x) at b8:27:eb:yy:yy:yy [ether] on wlp2s0
  ```
  
**On Windows:**
  ```
arp -a | findstr b8-27-eb
  ```
If this doesn’t work and you are using the latest Raspberry Pi 4, instead run:
  ```
arp -a | findstr dc-a6-32
  ```
If this doesn’t work and you are using the latest Raspberry Pi 4, instead run:
  ```
arp -a | findstr e4-5f-01
  ```
This will return an output similar to:
  ```
xx.xx.xx.x       b8-27-eb-yy-yy-yy     dynamic
  ```

Where the x’s are the IP address of any Raspberry Pi connected to the local network. Note it down.

If the command doesn’t return an IP address, you may need to wait a little longer for your Pi to join the network. If you still can’t see it after a few tries, which can happen with some home or office network configurations, we recommend you use a USB keyboard and HDMI screen to interact with your device.
  
* You should now be able to reach your Pi withan IP address like `192.168.0.20`.

### Access with Secure Shell

Now it’s time to connect to the Pi via SSH and get to work.
For that, a Secure Shell (SSH) client is needed.

If you need to provide connection details, use the following settings:

* host name: The ip address like `192.168.0.20`
* port: `22`
* username: `ubuntu`
* password:  `ubuntu`

Install and start the SSH client for your operating system:

* Windows: PuTTY ([Website](https://www.putty.org){:target="_blank"})
* MacOS and Linux: from the Terminal, use the native command:
  * `ssh pi@192.168.0.20`

🔍 *more: [using SSH with Raspberry Pi](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md){:target="_blank"}*


### Changing the password
When prompted, use “ubuntu” for the password. **(The cursor won’t move when you type the password.)** The first thing Ubuntu will do is to ask you to change it to a secure password. Once done, you can reconnect again with the SSH command and the new password.

Success! You are now connected to Ubuntu Server running on your Raspberry Pi.


### Still not connected to the internet?
Sometimes the structure of the network-config file that you modified before boot gets messed up when you boot. This would cause your Pi not to be able to connect to your wifi.

To fix this you need to go into the correct file and correct the issue. Run:
  ```
sudo nano /etc/netplan/50-cloud-init.yaml
  ```
so you can edit the file. Edit it so it looks like this again, the indentation needs to be correct for the .yaml to work:
  ```
wifis:
  wlan0:
    dhcp4: true
    optional: true
    access-points:
      "home network":
        password: "123456789"
  ```
Save and exit the file with   ``` Ctrl + S ``` and   ``` Ctrl + X ```.

Run   ``` sudo netplan apply ``` and   ``` sudo reboot ``` and when you log back in, you will be connected to your wifi.

---

## The command line

We are going to work on the command line of the Pi, which may be new to you.
Find some basic information below, it will help you navigate and interact with your Pi.

You enter commands and the Pi answers by printing the results below your command.
To make it clear where a command begins, every command in this guide starts with the `$` sign. The system response is marked with the `>` character.

In the following example, just enter `ls -la` and press the enter/return key:

```sh
$ ls -la
> example system response
# This is a comment, don't enter this on the command line
```

* **Auto-complete commands**:
  When you enter commands, you can use the `Tab` key for auto-completion, eg. for commands, directories or filenames.

* **Command history**:
  by pressing ⬆️ (arrow up) and ⬇️ (arrow down) on your keyboard, you can recall your previously entered commands.

* **Common Linux commands**:
  For a very selective reference list of Linux commands, please refer to the [FAQ](raspibolt_faq.md) page.

* **Use admin privileges**:
  Our regular user has no admin privileges.
  If a command needs to edit the system configuration, we need to use the `sudo` ("superuser do") command as prefix.
  Instead of editing a system file with `nano /etc/fstab`, we use `sudo nano /etc/fstab`.

  For security reasons, the user "bitcoin" cannot use the `sudo` command.

* **Using the Nano text editor**:
  We use the Nano editor to create new text files or edit existing ones.
  It's not complicated, but to save and exit is not intuitive.

  * Save: hit `Ctrl-O` (for Output), confirm the filename, and hit the `Enter` key
  * Exit: hit `Ctrl-X`

* **Copy / Paste**:
  If you are using Windows and the PuTTY SSH client, you can copy text from the shell by selecting it with your mouse (no need to click anything), and paste stuff at the cursor position with a right-click anywhere in the ssh window.

  In other Terminal programs, copy/paste usually works with `Ctrl`-`Shift`-`C` and `Ctrl`-`Shift`-`V`.

<script id="asciicast-mfa3ZyTE3K1RdbAXowaFjEJZK" src="https://asciinema.org/a/mfa3ZyTE3K1RdbAXowaFjEJZK.js" async></script>

---

## Working on the Raspberry Pi

You are now on the command line of your own Bitcoin node.
Let's start with the configuration.

### RaspiNail Configuration

* You can give your node a cute hostname like “raspinail” with
```sh
$ sudo hostnamectl set-hostname raspinail 
```
* Set your local timezone by copying your time zone from the list ```timedatectl list-timezones``` and then: 
```sh
$ sudo timedatectl set-timezone Europe/Madrid
``` 
with "Europe/Madrid" as an example.

The following two potential error messages are expected:

* After changing the hostname, e.g. to `raspinail`, a reboot is required to get rid of this error message.
  It can be safely ignored for now.

  ```
  sudo: unable to resolve host ubuntu: Name or service not known
  ```

* We will generate the corresponding ```locale``` files:
  ```sh
  $ sudo dpkg-reconfigure locales
  ```
  We will select in the first menu ```All locales``` when asked "Locales to be generated", and in the second menu ```None``` for "Default locale for the system environment".  
  
  If the above fix does not remove the locale error, it is probably your host machine that you use to SSH from pushing its locale onto the Pi. What you need to do is very simple: make Pi stop accepting locale over SSH regardless of origin. You do that by editing sshd_config file in nano editor:

```sh
  $ sudo nano /etc/ssh/sshd_config
  ```
All you need to do now is find the AcceptEnv LANG LC_* and make sure to comment it out so it looks like this:
```sh
  #AcceptEnv LANG LC_*
  ```
Now CTRL+X (save) and exit, and the error will be gone.

### Software update

It is important to keep the system up-to-date with security patches and application updates.
The “Advanced Packaging Tool” (apt) makes this easy.

💡 Do this regularly every few months to get security related updates.

```sh
$ sudo apt update
$ sudo apt full-upgrade
```

Make sure that all necessary software packages are installed:

```sh
$ sudo apt install raspi-config rpi-eeprom avahi-daemon htop git curl bash-completion jq qrencode dphys-swapfile hdparm --install-recommends
```
* We have just installed Avahi, so now your node is accessible by its hostname using raspinail.local instead of a dynamic IP inside your network, which is very handy if the router changes its IP by DHCP.

**On Windows:**  
  ```
ping raspinail.local

Pinging raspinail.local [fe80::xxxx:xxxx:xxxx:xxxxxx] with 32 bytes of data:
Reply from fe80::xxxx:xxxx:xxxx:xxxxxx: time<1ms
Reply from fe80::xxxx:xxxx:xxxx:xxxxxx: time<1ms
Reply from fe80::xxxx:xxxx:xxxx:xxxxxx: time<1ms
Reply from fe80::xxxx:xxxx:xxxx:xxxxxx: time<1ms

Ping statistics for fe80::xxxx:xxxx:xxxx:xxxxxx:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 0ms, Average = 0ms
  ```
Now your node is accessible using **raspinail.local** instead of a dynamic IP inside your network.

* We will do some cleaning:
```sh
$ sudo apt --fix-broken install
$ sudo apt autoremove
```
and then reboot the system:
```sh
$ sudo reboot
```

* Enter the folowing command ```raspi-config```, then choose ```6 Advanced options``` and then ```A1 Expand Filesystem```, to allow the system to make use of the full sdcard. It will reboot the system afterwards.

* Enter the folowing command ```raspi-config```, then choose ```6 Advanced options```, then ```A7 Boot ROM Version``` and then ```E1 Latest```, to update the Bootloader of the Raspberry. It will reboot the system afterwards.


### Add users

This guide uses the main user "admin" instead of "ubuntu" to make it more reusable with other platforms.

* Create the new user "admin", set `password [A]` and add it to the group "sudo"

  ```sh
  $ sudo adduser admin --ingroup sudo
  $ sudo adduser admin sudo
  ```

* And while you’re at it, change the password of the “root” admin user to your `password [A]`.

  ```sh
  $ sudo passwd root
  ```

The bitcoin and lightning processes will run in the background (as a "daemon") and use the separate user “bitcoin” for security reasons.
This user does not have admin rights and cannot change the system configuration.

* Enter the following command, set your `password [A]` and confirm all questions with the enter/return key.

  ```sh
  $ sudo adduser bitcoin
  ```

* For convenience, the user "admin" is also a member of the group "bitcoin", giving it read-only privileges to configuration files.

  ```sh
  $ sudo adduser admin bitcoin
  ```

* Restart your RaspiNail.

  ```sh
  $ sudo reboot
  ```

<script id="asciicast-8uhMDvDcDNf3cUT6A3FcqN4lo" src="https://asciinema.org/a/8uhMDvDcDNf3cUT6A3FcqN4lo.js" async></script>

---

## Attach external drive

To store the blockchain, we need a lot of space.
As a server installation, the Linux native file system Ext4 is the best choice for the external hard disk, so we will format the hard disk, erasing all previous data.
The external hard disk is then attached to the file system and can be accessed as a regular folder (this is called “mounting”).

🚨 **Existing data on this drive will be deleted!**

### Log in as "admin"

* Do not yet connect the external drive to your Pi, we need to check some things first.
* Start your Raspberry Pi by unplugging it and connecting the power cable again.
* Log in using SSH, but now with the user `admin`, your `password [A]` and the new hostname (e.g. `raspinail.local`) or the IP address.

  ```sh
  $ ssh admin@raspinail.local
  ```

* To change system configuration and files that don't belong to the "admin", you have to prefix commands with `sudo`.
  You will be prompted to enter your admin password from time to time for increased security.

### Make sure USB3 is performant

The Raspberry Pi 4 supports USB3 drives, but is very picky.
A lot of USB3 adapters for external drives are not compatible and need a manual workaround to be usable.
We will now check if your drive works well as-is, or if additional configuration is needed.

🔍 *more: [Raspberry Pi forum: bad performance with USB3 SSDs](https://www.raspberrypi.org/forums/viewtopic.php?f=28&t=245931){:target="_blank"}*

* First, lets get some information about your drive from the kernel messages.
  Clear the kernel buffer, and follow the new messages (let the last command run):

  ```sh
  $ sudo dmesg -C
  $ sudo dmesg -w
  ```

* Connect your external drive to the blue USB3 ports of the running Raspberry Pi, preferably with a good cable that came with the drive.

  Once the system recognizes it, details are automatically displayed by the `dmesg` command.

  ```
  [  726.547907] usb 2-1: new SuperSpeed Gen 1 USB device number 3 using xhci_hcd
  [  726.579304] usb 2-1: New USB device found, idVendor=152d, idProduct=0578, bcdDevice= 3.01
  [  726.579321] usb 2-1: New USB device strings: Mfr=1, Product=2, SerialNumber=3
  [  726.579333] usb 2-1: Product: USB 3.0 Device
  [  726.579346] usb 2-1: Manufacturer: USB 3.0 Device
  [  726.579357] usb 2-1: SerialNumber: 000000005B3E
  [  726.582254] usb 2-1: UAS is blacklisted for this device, using usb-storage instead
  [  726.582350] usb 2-1: UAS is blacklisted for this device, using usb-storage instead
  [  726.582364] usb-storage 2-1:1.0: USB Mass Storage device detected
  [  726.582674] usb-storage 2-1:1.0: Quirks match for vid 152d pid 0578: 1800000
  [  726.582783] scsi host0: usb-storage 2-1:1.0
  [  727.598422] scsi 0:0:0:0: Direct-Access     INTENSO  SATA III SSD     0301 PQ: 0 ANSI: 6
  [  727.599182] sd 0:0:0:0: Attached scsi generic sg0 type 0
  [  727.605796] sd 0:0:0:0: [sda] 937703088 512-byte logical blocks: (480 GB/447 GiB)
  [  727.606519] sd 0:0:0:0: [sda] Write Protect is off
  [  727.606536] sd 0:0:0:0: [sda] Mode Sense: 47 00 00 08
  [  727.607982] sd 0:0:0:0: [sda] Disabling FUA
  [  727.607998] sd 0:0:0:0: [sda] Write cache: enabled, read cache: enabled, doesn't support DPO or FUA
  [  727.611337]  sda: sda1
  [  727.614890] sd 0:0:0:0: [sda] Attached SCSI disk
  ```

* Make a note of the values shown for `idVendor` and `idProduct` (in this case "152d" and "0578").
  Then, exit `dmesg` with `Ctrl`-`C`.

* List all block devices with additional information.
  The list shows the devices (e.g. `sda`) and the partitions they contain (e.g. `sda1`).

  Make a note of the partition name you want to use (in this case "sda1").

  ```sh
  $ lsblk -o NAME,MOUNTPOINT,UUID,FSTYPE,SIZE,LABEL,MODEL
  > NAME        MOUNTPOINT UUID                                 FSTYPE   SIZE LABEL  MODEL
  > sda                                                                447.1G        SATA_III_SSD
  > └─sda1                 9ec0b784-d448-4757-a3b2-8abd57c544f3 ext4   447.1G
  > mmcblk0                                                             14.9G
  > ├─mmcblk0p1 /boot      5203-DB74                            vfat     256M boot
  > └─mmcblk0p2 /          2ab3f8e1-7dc6-43f5-b0db-dd5759d51d4e ext4    14.6G rootfs
  ```

* If your external drive (e.g. `sda`) does not contain any partitions (e.g. no `sda1`), create a partition first using as described here:
  <https://superuser.com/questions/643765/creating-ext4-partition-from-console>

### Create an EXT4 partition on the external disk, if not already created:
* First, check if there ir already a partition of the Linux type (EXT4) already present with   ``` fdisk -l /dev/sda  ```.
* If not, proceed with ```sudo fdisk /dev/sda```, then press ```n```, the press ```Enter``` four times to create the partition. Press ```w``` to write it to the disk. If already inside the fdisk menu, exit to the main prompt with ```q```.
* Create the filesystem on the partition with ```sudo mkfs.ext4 /dev/sda1```


* Now, let's test the read performance of your drive.
  Make sure to use the right partition name (used with the `/dev/` prefix).

  ```sh
  $ sudo hdparm -t --direct /dev/sda1

  /dev/sda1:
  Timing O_DIRECT disk reads:   2 MB in 31.18 seconds =  65.69 kB/sec
  ```

* In this case, the performance is really bad: 65 kB/sec is so 1990's.
  If the measured speed is more than 50 MB/s, you can skip the rest of this section and go directly to formatting the external drive.

  Otherwise we need to configure the USB driver to ignore the UAS interface of your drive.
  This configuration must be passed to the Linux kernel on boot:

  * Open the `cmdline.txt` file of the bootloader.

    ```sh
    $ sudo nano /boot/cmdline.txt
    ```

  * At the start of the line of parameters, add the text `usb-storage.quirks=aaaa:bbbb:u` where `aaaa` is the "idVendor" and `bbbb` is the "idProduct" value.
    Make sure that there is a single space character (` `) between our addition and the next parameter.
    Save and exit.

    ```
    usb-storage.quirks=152d:0578:u ..............
    ```

  * Reboot the Raspberry Pi with the external drive still attached.

    ```sh
    $ sudo reboot
    ```

  * After you logged in as "admin" again, let's test the read performance once more.

    ```sh
    $ sudo hdparm -t --direct /dev/sda1

    /dev/sda1:
    Timing O_DIRECT disk reads: 510 MB in  3.01 seconds = 169.59 MB/sec
    ```

  * You should see a significant increase in performance.
  If the test still shows a very slow read speed, your drive or USB adapter might not be compatible with the Raspberry Pi.
  In that case I recommend visiting the Raspberry Pi [Troubleshooting forum](https://www.raspberrypi.org/forums/viewforum.php?f=28){:target="_blank"} or simply try out hardware alternatives.

<script id="asciicast-NiOhoAsu2g9kltfHXzfU6GLnq" src="https://asciinema.org/a/NiOhoAsu2g9kltfHXzfU6GLnq.js" async></script>

### Format external drive and mount

* Format the partition on the external drive with Ext4 (use `[NAME]` from above, e.g `sda1`)

  ```sh
  $ sudo mkfs.ext4 /dev/[NAME]
  ```

* List the devices once more and copy the `UUID` into a text editor on your local computer.

  ```sh
  $ lsblk -o NAME,MOUNTPOINT,UUID,FSTYPE,SIZE,LABEL,MODEL
  ```

* Edit the `fstab` file and add the following as a new line at the end, replacing `123456` with your own `UUID`.

  ```sh
  $ sudo nano /etc/fstab
  ```

  ```
  UUID=123456 /mnt/ext ext4 rw,nosuid,dev,noexec,noatime,nodiratime,auto,nouser,async,nofail 0 2
  ```

  🔍 *more: [complete fstab guide](http://www.linuxstall.com/fstab){:target="_blank"}*

* Create the directory to add the hard disk and set the correct owner

  ```sh
  $ sudo mkdir /mnt/ext
  ```

* Mount all drives and check the file system. Is “/mnt/ext” listed?

  ```sh
  $ sudo mount -a
  $ df -h /mnt/ext
  > Filesystem      Size  Used Avail Use% Mounted on
  > /dev/sda1       440G   73M  417G   1% /mnt/ext
  ```

### Create bitcoin directory

* Set the owner

  ```sh
  $ sudo chown -R bitcoin:bitcoin /mnt/ext/
  ```

* Switch to user "bitcoin", navigate to the external drive and create the bitcoin directory.

  ```sh
  $ sudo su - bitcoin
  $ cd /mnt/ext
  $ mkdir bitcoin
  $ ls -la
  > total 28
  > drwxr-xr-x 4 bitcoin bitcoin  4096 Dec 12 17:43 .
  > drwxr-xr-x 4 root    root     4096 Dec 12 17:38 ..
  > drwxr-xr-x 2 bitcoin bitcoin  4096 Dec 12 17:43 bitcoin
  > drwx------ 2 bitcoin bitcoin 16384 Dec 12 17:30 lost+found
  ```

* Create a testfile in the new directory and delete it.

  ```sh
  $ touch bitcoin/test.file
  $ rm bitcoin/test.file
  ```

  If this command gives you an error, chances are that your external hard disk is mounted as “read only”.
  This must be fixed before proceeding.
  If you cannot fix it, consider reformatting the external drive.

* Exit the "bitcoin" user session

  ```sh
  $ exit
  ```

<script id="asciicast-c75NG00m72iaguULOzzVc9Z3v" src="https://asciinema.org/a/c75NG00m72iaguULOzzVc9Z3v.js" async></script>

🔍 *more: [external storage configuration](https://www.raspberrypi.org/documentation/configuration/external-storage.md){:target="_blank"}*

---

## Move swap file

The usage of a swap file can degrade your SD card very quickly.
Therefore, we will move it to the external drive.

* Edit the configuration file and replace existing entries with the ones below. Save and exit.

  ```sh
  $ sudo nano /etc/dphys-swapfile
  ```

   ```ini
   CONF_SWAPFILE=/mnt/ext/swapfile

   # comment or delete the CONF_SWAPSIZE line. It will then be created dynamically
   #CONF_SWAPSIZE=
   ```

* Recreate new swapfile on ssd (will be active after reboot)

  ```sh
  $ sudo dphys-swapfile install
  ```

<script id="asciicast-p7I8GeTfxOk15dFWHu8FVV83q" src="https://asciinema.org/a/p7I8GeTfxOk15dFWHu8FVV83q.js" async></script>

---

Next: [Security >>](raspibolt_21_security.md)
