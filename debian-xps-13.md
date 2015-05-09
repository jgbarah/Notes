# Installing Debian testing (stretch) in a Dell XPS 13 Developer Edition (2015)

I got my shinny new XPS 13, with BIOS version A03 (which at this time seems to solve most of the issues that previous BIOS had with Ubuntu). There is a [detailed description of the laptops in this family at George Barton's blog](http://bartongeorge.net/2015/04/09/4th-gen-dell-xps-13-developer-edition-available/#comment-94369), including the software they have and some other nuisances.

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

At this point I was done with Ubuntu, and started with Debian. My intention was to install Debian testing (stretch, as of May 2015).

## First install of Debian and UEFI trouble

I started with the [very detailed instructions by Eric Mill](https://github.com/konklone/debian/blob/master/installing.md).

## Some more useful information

* [Dell XPS 13 (2015) page for ArchLinux](https://wiki.archlinux.org/index.php/Dell_XPS_13_%282015%29).
