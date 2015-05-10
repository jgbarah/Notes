# Installing Debian testing (stretch) in a Dell XPS 13 Developer Edition (2015)

I got my shinny new XPS 13 9343, with BIOS version A03 (which at this time seems to solve most of the issues that previous BIOS had with Ubuntu). There is a [detailed description of the laptops in this family at George Barton's blog](http://bartongeorge.net/2015/04/09/4th-gen-dell-xps-13-developer-edition-available/), including the software they have and some other nuisances.

## Getting Ubuntu to work

The system came with Ubuntu preinstalled (no Windows, ;-) ). Well, in fact, it came with Ubuntu 14.04 in the hard disk, prepared to be installed, but not installed.

When you power it for the first time , you follow a procedure to install and configure that Ubuntu from what is available in the hard disk. I run through that process. One of the questions you hav to answer is whether you want it to produce some media (USB stick) to be able of recovering the factory version of Ubuntu that comes with the laptop. Unfortunately, I answered "yes". That caused a lot of headaches.

The problems seems to be a but in the Dell tool for producing the factory-software media is broken. That media is not produced, and what is worse, it leaves some remains (a file or something), that when you run the process again, prevents the process of completing nicely, even after reboots.

The only solution I found was to boot in console mode in the semi-installed Ubuntu (which was installed enough to let me in). Then run:

```
apt-get install -f
apt-get update
apt-get upgrade
```

Fortunately, a new version of the Dell installed came with the upgrade, which let me produce the media with the Ubuntu that came in the box.

As a side effect, Ubuntu seemed now to be completely installed and working, although I didn't check it a lot.

## First install of Debian and UEFI trouble

At this point I was done with Ubuntu, and started with Debian. My intention was to install Debian testing ([stretch](https://www.debian.org/releases/stretch/), as of May 2015).

I started with the [very detailed instructions by Eric Mill](https://github.com/konklone/debian/blob/master/installing.md), with some differences:

* Instead of the RC netinst boot image, I used the [current daily snapshot of the Debian installer] (https://www.debian.org/devel/debian-installer/), selecting the [image for amd64](http://cdimage.debian.org/cdimage/daily-builds/daily/arch-latest/amd64/iso-cd/) (as of today, May 9th).
* For having access to the Internet with netinst, I used an Android phone conected to my WiFi network, and tethered to the laptop via USB.
* After following the procedure with the netinst installation, the system (configured as UEFI without Secure Boot) didn't find the boot partition in the hard disk. I created a bootable USB stick with the [rEFInd Boot Manager](http://www.rodsbooks.com/refind/getting.html), using the [USB flash drive image](http://sourceforge.net/projects/refind/files/0.8.7/refind-flashdrive-0.8.7.zip/download). It is enough to just boot from it: it automatically recognized the boot partition installed by the Debian netinst in the hard drive, and booted it.
* In /etc/apt/sources.list, I already have stretch, so I only add "contrib non-free" in each line, for getting access to firmware for the WiFi chipset, and some other non-free stuff.
* For WiFi, I run "sudo apt-get install broadocom-sta-dkms"
* I run "sudo dpkg-reconfigure console-setup" to increase the font in the TTY (non-X consoles).

## Current status

* UEFI boot: not working, I'm booting with a USB stick with rEFInd Boot Manager
* WiFi (Broadcom): ok
* Sound: ok
* Suspend: ok
* Camera: ok
* Microphone: not working

## Some more useful information

* [Dell XPS 13 (2015) page for ArchLinux](https://wiki.archlinux.org/index.php/Dell_XPS_13_%282015%29).
* [Debian on Dell XPS 13 9343](https://wiki.debian.org/InstallingDebianOn/Dell/Dell%20XPS%2013%209343).
* [DELL xps13 2015 (9343) Linux Support](https://github.com/mpalourdio/xps13).
