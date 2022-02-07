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

