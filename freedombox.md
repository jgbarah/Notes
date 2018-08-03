# Installing and managing Freedom Box

I had a spare Raspberri Pi 3 Model B, and I decided to give it a try with the software produced by the [FreedomBox project](https://freedombox.org/). I want to check to which extent it could be used for hosting services that most people use "from the cloud".

My complete environment is:

* Home network, with a simple WiFi and Ethernet capable router. I connect my Raspberri Pi to it, via Ethernet.
* Raspberri Pi Model B, for running FreedomBox.
* Debian box, for preparing stuff, and initial connection to the FreedomBox. It runs Debian/testing.

## Installation

Once nice thing about Raspberri Pi is that it boots from a SD card. All you need to do to test a new system is to produce an new SD card that boots it, and insert it in the device. Once I found out which image I had to use for producing a boot Raspberri Pi SD card, the process was absurdly easy.

Raspberri Pi 3 is not a device supported by FreedomBox stable, apparently because it needs some proprietary software for booting (at least when using Ethernet). But you can find [an entry about it](https://wiki.debian.org/FreedomBox/Hardware/RaspberryPi3) in the [FreedomBox wiki](https://wiki.debian.org/FreedomBox/). In that entry, you can find out that bootable images for this device are available only for the [testing distribution downloads](https://freedombox.org/download/testing/) (and not for the stable one). The image I used was [freedombox-testing-nonfree_latest_raspberry3-armhf.img.xz](https://ftp.freedombox.org/pub/freedombox/testing-latest/freedombox-testing-nonfree_latest_raspberry3-armhf.img.xz).

For producing the SD card, I followed the instructions in [Downloading images](https://wiki.debian.org/FreedomBox/Download#Downloading_Images), running the checks on the integrity and signatures of the files and all that. Then, I uncompressed the downloaded image, and copied it to the SD card. My card is a 16 GB one, but I guess a 4 GB one would be enough. In my Debian box, I run:

```bash
$ xz -d freedombox-testing-nonfree_latest_raspberry3-armhf.img.xz
$ sudo dd bs=1M if=freedombox-testing-nonfree_latest_raspberry3-armhf.img of=/dev/mmcblk0 conv=fdatasync
$ sudo sync
```

My SD card in Debian was mounted as `/dev/mmcblk0`. I guess the third line is not really needed, since `dd` is already syncing, but just in case.

Then, I just removed the SD card from my Debian box, inserted it in the Raspberri Pi, connected it to an spare Ethernet port in my home router, powered it, and it started to run! This was all with respect to installation. See below how I learned that it had booted correctly, and how I accessed it.


## Finding and configuring FreedomBox

So I had my brand new FreedomBox up and running, but I still didn't know it. Following [instructions in the How to get Started section in the manual](https://wiki.debian.org/FreedomBox/Manual#FreedomBox.2FManual.2FQuickStart.How_to_get_started), I used mDNS to access the device. For that, I had to install mDNS in my Debian box, following [instructions in the ZeroConf for Debian wiki](https://wiki.debian.org/ZeroConf):

```bash
$ apt-get install avahi-daemon avahi-discover libnss-mdns
```

Once this was done, I just browsed to [http://freedombox.local/](http://freedombox.local/), and I got the FreedomBox welcome message in my browser.

Just to test it, I also used `nmap` to find out the IP address that my router had allocated for the device:

```bash
$ nmap -p 80 --open -sV 192.168.1.0/24
Starting Nmap 7.70 ( https://nmap.org ) at 2018-08-03 22:05 CEST
...
Nmap scan report for 192.168.1.75
Host is up (0.014s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.33
Service Info: Host: 127.0.1.1

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 256 IP addresses (8 hosts up) scanned in 10.26 seconds
```

(some other web servers were found, of other devices in my network, but this is the relevant part).

Now, the configuration is just following instructions in the browser. I need to provide a user and password, and that's all. Pretty simple. Now I'm ready to install apps in my FreedomBox!

![FreedomBox apps](freedombox-apps.png)


## Some useful links

* [FreedomBox project](https://freedombox.org/)
* [FreedomBox wiki](https://wiki.debian.org/FreedomBox/)
* [FreedomBox manual](https://wiki.debian.org/)