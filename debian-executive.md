# Installing debian testing (bookworm) in a Slimbook Executive

Finally I have my [Slimbook Executive](https://slimbook.es/en/executive-en).
I ordered it with Debian, but after receiving it, and testing that it
seemed to work well with Debian 10.0, the version that had installed,
I decided to move to Debian testing, which is my usual system.
When this happened, early February 2022, testing is bookworm.

These are some notes on installing Debian testing on this Slimbook Executive.

## Basic installation

I follow instructions in the [Debian testing Wiki page](https://wiki.debian.org/DebianTesting). For details, I referred to the [instruccions for installing the latest stable](https://www.debian.org/releases/stable/amd64/) (bullseye).

I use the `netsinst` image for `amd64` (for Intel-like 64 bits architectures), from the [weekly install images with non-free firmware](http://cdimage.debian.org/cdimage/unofficial/non-free/cd-including-firmware/weekly-builds/amd64/jigdo-cd/) using jigdo. Apparently, there is some WiFi devices needing non-free firmware. First step is to download it, and build a bootable USB stick with it. From my current Debian testing machine:

```
sudo apt install jigdo-file
jigdo-lite http://cdimage.debian.org/cdimage/unofficial/non-free/cd-including-firmware/weekly-builds/amd64/jigdo-cd/firmware-testing-amd64-netinst.jigdo
mv firmware-testing-amd64-netinst.iso firmware-bookworm-amd64-netinst.iso
cp firmware-bookworm-amd64-netinst.iso /dev/sda
sync
```

When running `jigdo-lite` I just answered everything accepting the detaults. The, I moved the retreived ISO file to a new name just for convenience. Finally, I copied the ISO file to a USB stick, which sits in `/dev/sda`.

Now, I plug this USB stick in the laptop. When booting, I repeatedly press the F2 key, to enter the BIOS menu. There, I select USB stick as the first option for booting. And boot with the Debian installer.

## Fixing flickering when pressing any key

Upon booting, things seem to work, but there is a lot of flickering, almost any time I press a key. I contact Slimbook technical support, and they recommend disabling PSR. Apparently, from what I learned, Panel Self Refresh (PSR) is the cause, and until it is fixed, i need to disable it. For that, I edit `/etc/default/grub`, adding `i915.enable_psr=0` to `GRUB_CMD_LINE_LINUX_DEFAULT`:

```
# i915.enable_psr=0 is for avoiding flikering when pressing any key (temporary solution)
GRUB_CMD_LINE_LINUX_DEFAULT="quiet i915.enable_psr=0"
```

Then, run (as root) `update-grub`, and reboot. And no more spurious flickering.

## Install Slimbook applications

[Maybe this is no longer needed. In particular, in Gnome 42 now I have a menu selecting between three performance mode, and some tweeks about performance for optimizing battery usage]

There are some [Slimbook applications](https://slimbook.es/tutoriales/aplicaciones-slimbook) potentially interesting in a [Slimbook PPA](https://launchpad.net/~slimbook/+archive/ubuntu/slimbook). But it includes packages only for Ubuntu. I decide to get some of them, as source code, and rebuild them for Debian testing.

First, I create a new APT file, `/etc/apt/sources.list.d/slimbook-ubuntu-slimbook-impish.list`:

```
# Slimbook apps: source code for Ubuntu Impish (21.10)
deb-src [trusted=yes] https://ppa.launchpadcontent.net/slimbook/slimbook/ubuntu impish main
```

Note: `trusted=yes` means that apt is going to install from that repository without checking signatures and other security measures. This would allow for person-in-the-middle attacks. So, use at your own risk.

Then, I update packages, get the source code for the packages I want, and then build the package. Finally, install it:

```
sudo apt install apt-src
sudo apt-src update
cd software
```

and then

```
apt-src install slimbookbattery
apt-src build slimbookbattery
```

or, in a single step:

```
apt-src --build install slimbookbattery
```

For installing:

```
sudo dpkg --install slimbookbattery_4.0.4_all.deb
```

or

```
sudo apt install ./slimbookbattery_4.0.4_all.deb
```

Then, for it to run, follow instructions in [I do not see the Slimbook Battery indicator in the taskbar](https://github.com/slimbook/slimbookbattery#i-do-not-see-the-slimbook-battery-indicator-in-the-taskbar) and in the [Slimbook Battery tutorial](https://slimbook.es/en/tutoriales/aplicaciones-slimbook/520-slimbook-battery-4-application-to-optimize-your-laptop-s-battery):

```
sudo apt-get install gnome-shell-extension-appindicator
```

Then reload the desktop (or reboot), and run "Extensions", enabling "Ubuntu AppIndicators".

Other apps: slimbookintelcontroller slimbookgestures slimbookface

## Configuring HiDPI

I'm having trouble finding the right size for fonts and icons on screen. This seems to have to do with the screen being HiDPI. An obvious way out is to configure the screen to a lower resolution, but I'd prefer to avoid it. So, I try several options:

* GNOME Settings. Displays, scale to 200%.

* GNOME Tweaks. Fonts, scaling factor to 1.7

In Wayland, this seems to work well. Unfortunately, in XWindow it doesn't: some fonts are pretty big (apparentely, both scale and font scaling factor are being used).

## Extend swap for having hibernation

The system as installed is not hibernating. It seems the problem is the swap partition is not large enough. The Debian standard installation produced a 1 GB swap, but with 64 GB, that's not large enough. I'm going to use a swap file to enlarge swap space.

I follow instructions from [Cómo habilitar la hibernación en Ubuntu](https://slimbook.es/en/tutoriales/linux/448-como-habilitar-la-hibernacion-en-ubuntu-18-04-lts-19-10-20-04-o-linux-mint-y-derivadas)

```
sudo fallocate -l 64G /swap_file
sudo chmod 600 /swap_file
sudo mkswap /swap_file
echo "/swap_file  swap   swap   defaults   0 0" | sudo tee --append /etc/fstab
sudo swapon /swap_file
```

Then, verify all went well:

```
free -m
sudo swapon -s
```

**Note:** This would not be needed if had produced a 64 GB swap partition, instead of the current one.

Now, check the UUID for the swap device:

```
$ sudo findmnt -no SOURCE,UUID -T /swap_file
16be1f13-fc06-428c-9b21-3345747c3eaf
```

The obtained UUID will be used later.

And the offset of that file:

```
sudo filefrag -v /swap_file | awk '$1=="0:" {print substr($4, 1, length($4)-2)}'
104392704
```

Now, edit `/etc/default/grub`, adding this to the end of GRUB_CMDLINE_LINUX_DEFAULT: `resume=UUID=UUID resume_offset=OFFSET`. In our case, this will be:

```
resume=UUID=16be1f13-fc06-428c-9b21-3345747c3eaf resume_offset=104392704
```

Finally, reconfigure grub to have into account the change:

```
sudo update-grub
```

A final touch: instead of hibernate, `suspend-then-hibernate` can be used. It will suspend the laptop, but after the specified amount of time, will hibernate it. Following the instructions in ["Lid Closed Suspend then Hibernate"](https://askubuntu.com/questions/1072504/lid-closed-suspend-then-hibernate) you need to specify for how long to wait until hibernation, by including in `/etc/systemd/sleep.conf`:

```
[Sleep]
HibernateDelaySec=180min
```

Then, just run

```
systemctl suspend-then-hibernate
```

This can be configured so that it happens whenever the lid is closed, by editing `/etc/systemd/logind.conf` and uncommenting / adding the following parameter:

```
HandleLidSwitch=suspend-then-hibernate
```

Now, restart and it works!


## Resetting the GNOME configuration

```
mkdir old-config
mv .config/dconf/user old-config
```
