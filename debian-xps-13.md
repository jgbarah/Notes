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

## Fixing UEFI booting

The fix to the problem of not booting from hard drive has been renaming the Debian UEFI loader to the default name, as explained in [rEFInd (Alternative Naming Options)](http://www.rodsbooks.com/refind/installing.html#naming). What I did was in fact not renaming, but copying the Debian loader:

```
cd /boot/efi/EFI
cp -r debian BOOT
mv BOOT/grubx64.efi BOOT/bootx64.efi
shutdown -h now
```

Just to be on the safe side, I entered the BIOS (F2 when the Dell logo is onscreen, while booting), and reverted to "factory defaults" the UEFI boot settings.

On the next boot, it booted Debian from hard disk without a glitch.

## HiDPI

The screen has a high resolution. There is some configuration to be done so that fonts and applications in general do not appear too small in the screen.

### Firefox

Instructions from [Improving Linux HiDPI Support For Gnome, KDE, Xfce, Cinnamon And Firefox](http://www.makeuseof.com/tag/linux-hidpi-support-for-gnome-kde-xfce-cinnamon-and-firefox/):

> entering about:config into the address bar, accepting the warning that appears, and then searching for layout.css.devPixelsPerPx. You can change the value for this parameter all the way up to 2, but I like it most at 1.5 or 1.75.

I use 1.75

### Gnome

```
gsettings set org.gnome.desktop.interface scaling-factor 0
```

You can  also use gnome-tweak-tool, Windows panel, HiDPI window scaling set to 0.

It seems 0 means "scaling should be done per monitor", which seems better than "2" (that can be used for gsettings, above, too), which means that all monitors will scale by 2. This single change made Atom work like a charm...

### Gimp

The only trouble with Gimp seem to be the icons, fonts for menus etc. are a bit small, but usable. To fix the icons, I downloaded the theme HiDPI.tar.gz, linked from [GIMP HiDPI symbolic icon theme](https://alexskc.wordpress.com/2015/05/09/gimp-hidpi-symbolic-icon-theme/). Then, it is just a matter of installing it where Gimp may find it:

```
cd ~/.gimp-2.8/themes
tar xvzf ~/Download/HiDPI.tar.gz
```

And then launching Gimp, and in the menu, "Edit|Preferences|Themes", select HiDPI theme, ready!

### TTY

Fixing consoles (accessed through CTR-ALT-Fn):

```
sudo dpkg-reconfigure console-setup
```

I selected 16x32 as a font, which seems to be the largest one.

### Grub boot menu

Following instruction in [ HiDPI on Ubuntu with a Samsung Ativ 9](http://www.obeythetestinggoat.com/ot-hidpi-on-ubuntu-with-a-samsung-ativ-9.html). generating a new font in 30-point:

```
sudo grub-mkfont -s 30 -o /boot/grub/DejaVuSansMono.pf2 /usr/share/fonts/truetype/dejavu/DejaVuSansMono.ttf
[ Some errors, hopefully not a trouble, about "Unknown gsub font feature..." ]
```

Edit /etc/default/grub:

```
GRUB_FONT=/boot/grub/DejaVuSansMono.pf2
```

And update grub boot:

```
sudo update-grub
```

## Still to do

Try the scripts here to properly handle multiple monitors: https://wiki.archlinux.org/index.php/HiDPI#External_displays

## Touchpad

Some configuration to enable palm detection, click in pad, and soft button at the bottom; and disable tap to click, which was a bit disturbing to me. The AccelFactor is supposed to avoid repeating keys. The configuration is based on the description found in [Touchpad Synaptics information for Arch Linux](https://wiki.archlinux.org/index.php/Touchpad_Synaptics). The file to add is /etc/X11/xorg.conf.d/50-synaptics.conf:

```
Section "InputClass"
    Identifier "touchpad catchall"
    Driver "synaptics"
    MatchIsTouchpad "on"
    # MatchDevicePath "/dev/input/event*"
        Option "AccelFactor" "0.2"
        #Option "RTCornerButton" "3"
        #Option "RBCornerButton" "0"
        #Option "LTCornerButton" "2"
        #Option "LBCornerButton" "0"
    	#Option "AreaTopEdge" "10"
        # Enables Palm Detection to prevent bad clicks
        # This seems to work on PS/2, but not on i2c
        Option "PalmDetect" "1"
        Option "PalmMinWidth" "8"
        Option "PalmMinZ" "100"
        # Enable clickpad support
        Option "ClickPad" "true"
        # Sets up soft buttons at the bottom
        # First 40% - Left Button
        # Middle 20% - Middle Button
        # Right 40% - Right Button
        Option "SoftButtonAreas" "60% 0 82% 0 40% 59% 82% 0"
        # Disables Tap to click
        Option "MaxTapTime" "0"
        # Enable two finger scrolling
        Option "VertTwoFingerScroll" "on"
        Option "HorizTwoFingerScroll" "on"
EndSection
```
## Configuring for SSD

My laptop enjoys a 250 GB SSD. Although I'm not completely sure that tweeking configuration makes a difference, after some days of use I finally find the time to make some configuration that should enlarge the live of the disk, after recommendations found in [SSD: how to optimize your Solid State Drive for Linux Mint 17.1, Ubuntu 14.04 and Debian](https://sites.google.com/site/easylinuxtipsproject/ssd) and elswhere.

Add noatime to /etc/fstab. After it, the line for my ext4 partition is like:

```
/dev/mapper/expisito--vg-root / ext4 noatime,errors=remount-ro 0 1
```

For now, I didn't enable TRIM. fstrim does not work out of the box:

```
# fstrim -v /
fstrim: /: the discard operation is not supported
```

I suspect this is due to the LVM / crypto layers, but I still didn't have time to research it.

## Sound

Sound input (microphone) didn't work until the kernel 4.1 came in. Now it works. I had to do nothing at all.

## Current status

* UEFI boot: ok
* WiFi (Broadcom): ok
* Sound: ok
* Suspend: ok
* Camera: ok
* Microphone: ok
* Touchpad: ok
* Touchscreen: works well on booting, stops working after suspend.

## Some more useful information

* [Dell XPS 13 (2015) page for ArchLinux](https://wiki.archlinux.org/index.php/Dell_XPS_13_%282015%29).
* [Debian on Dell XPS 13 9343](https://wiki.debian.org/InstallingDebianOn/Dell/Dell%20XPS%2013%209343).
* [DELL xps13 2015 (9343) Linux Support](https://github.com/mpalourdio/xps13).



