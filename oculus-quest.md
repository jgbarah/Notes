# Some stuff about Oculus Quest

## How to link from my Debian box

I wanted to link to the Quest from my Debian box, so that I can later debug from it. For that, I need:

* To activate developer mode in the Quest. That is done from the Oculus app in the phone (the one that controls the Quest). Option `Settings` > `More Settings` > `Tap on Developer Mode`.

* In the Debian box, I added my user to the `plugdev` group (assume the user is `user`):

```
$ sudo usermod -a -G plugdev user
```

* Logout from my Debian accouint, and log back in again. To check I'm in the group, from the shell I just run `id`, and check that `plugdev` is among the list of groups.

* Check if it works. I connect a USB cable to the Quest from my Debian box, and type:

```
$ lsusb
...
Bus 001 Device 059: ID 2833:0186 Oculus Quest
```

That means the device is connected and recognized. It is important that the Quest is on (ensure it didn't suspend).

* Final check is to install `adb` and use it to connect to the Quest:

```
$ sudo apt install adb
...
$ adb devices
List of devices attached
XXXXXXXXXXX	device
```

* In my case, i also had to write udev rules for having permissions on it with the `plugdev` group. I placed this file in `/etc/udev/rules.d/51-quest.rules`:

```
SUBSYSTEM=="usb", ATTR{idVendor}=="2833", ATTR{idProduct}=="0186", MODE="0660", 
GROUP="plugdev", SYMLINK+="quest%n"
```

The numbers `idVendor` and `idProduct` come from the `lsusb` listing: the numbers before and after the semicolon, right after `ID`.

Once i did this, unplug the USB cable and plug it again, `adb devices` worked like a charm. I also got convenient `/dev/quest?` links for the Quest, when it is connected.

Details and related documentation:

* [Oculus Developes: Debug your Browser Content](https://developer.oculus.com/documentation/oculus-browser/browser-remote-debugging/)
* [Adding udev rules for USB debugging Android devices](https://www.janosgyerik.com/adding-udev-rules-for-usb-debugging-android-devices/)