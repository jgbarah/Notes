# Installing debian testing (buster) in a Slimbook Katana II

Finally I have my [Katana II](https://slimbook.es/en/katana-ii-the-ultrabook-aluminum).
I ordered it with Debian, but after receiving it, and testing that it
seemed to work well with Debian 9.0, the version that had installed,
I decided to move to Debian testing, which is my usual system.

These are some notes on installing Debian testing on this Katana II.

## Basic installation

Since Debian 10 is close (I'm writing in March 2019),
I used the installers that are being prepared for it.
I used the network installed, and it worked like a charm.
No specific problem, except for needing the one supporting
proprietary firmware (for the WiFi card).

Everything seems to work: USB, WiFi, sound, camera...

## Getting rid of the TSC_DEADLINE message

Just before tring to mount the encrypted volume, I see this message,
produced by the kernel:

```
[Firmware Bug] TSC_DEADLINE disabled due to errata: please update
microcode to version: 0x52 (or later)
```

Fortunately, [the fix is easy](https://unix.stackexchange.com/questions/410854/tsc-deadline-disabled-due-to-errata#410873):

```
% sudo apt-get install intel-microcode
```


## Improving battery control

I found out about
[Slimbook Battery 3](https://slimbook.es/en/tutoriales/aplicaciones-slimbook/398-slimbook-battery-3-application-for-optimize-battery-of-your-laptop). I installed it, and it seems the battery like is increased by about 25%-30% (not exact accounts) wiht the default values for the balanced working model.

Installing it in Debian buster is a bit tricky, because python-appindicator is no longer available as a package. So, I used two packages from Debian strech: [python-appindicator_0.4.92-4_amd64.deb](http://ftp.de.debian.org/debian/pool/main/liba/libappindicator/python-appindicator_0.4.92-4_amd64.deb) and [libappindicator1_0.4.92-4_amd64.deb](http://ftp.de.debian.org/debian/pool/main/liba/libappindicator/libappindicator1_0.4.92-4_amd64.deb).

So, I downloaded both of them. Then, I added the Slimbook PPA repository to my list of apt repositories. And finally, I apt-installed some stuff that are dependencies for the `libappindicator1` and `python-appindicator` packages, those packages themselves (via `dpkg`) and finally `slimbookbattery`:

```
% sudo add-apt-repository "deb http://ppa.launchpad.net/slimbook/slimbook/ubuntu disco main"
% sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BE80F1EEB3838E61E42091B378A22399981017FC
% sudo apt-get update
% sudo apt-get install libdbusmenu-gtk4 libindicator7 python-gobject
% sudo dpkg -i libappindicator1_0.4.92-4_amd64.deb python-appindicator_0.4.92-4_amd64.deb
% sudo apt-get install slimbookbattery
```

It is important to follow this order, otherwise you can get inconsistent states in installed packages (that can be easily solved with `sudo apt --fix-broken install`, but anyway...).

To get the SlimbookBattery icon in the top icon bar, you also need to install the corresponding GNOME Shell extension, which is not installed by default:

```
% sudo apt-get install gnome-shell-extension-appindicator
```

After this, I run the configuration GUI, by searching the apps, and launching the one with the SlimbookBattery and the gear wheel.
The only option I change is, in Balanced Mode, "Wi-Fi disabled when not in use".
This seems to prevent that sometimes, when booting after hibernation, WiFi was not active.

After saving configuration (OK button), I see errors in the console like:

```
sh: 1: prime-select: not found
```

but it seems that's only a warning because I don't have NVIDIA graphics cards in my Katana II. Which is fine.

Now, I'm seeing an increasy in battery life of about 25%-30%. Nice.

To get the icon in the top bar, I still had to reboot the laptop (maybe rebooting the GNOME Shell would be enough), and run the GNOME Tweak Tool upon reboot to activate the opton for getting Appindicator notifications: menu "Extensions", option "KStatusNotifierItem/AppIndicator".

## Suspension problem

[ It seems this is no longer needed, after I installed and configured
`slimbookbattery`, see above. It includes exactly the same file
described below ;-) However, I'm letting the instructions here, just in case. ]

When suspending, batteries seem to drain rather quickly.
This can be fixed by making the laptop hibernate after staying
some time in suspension. I decide to do that after three hours in
suspension, which seems a good trade between battery duration and
booting quickly.

The process is described in
[SystemdSuspendSedation in the Debian Wiki](https://wiki.debian.org/SystemdSuspendSedation), and is easy. First, create this file:

```
# /etc/systemd/system/suspend-sedation.service
[Unit]
Description=Hibernate after suspend
Documentation=https://bbs.archlinux.org/viewtopic.php?pid=1420279#p1420279
Documentation=https://bbs.archlinux.org/viewtopic.php?pid=1574125#p1574125
Documentation=https://wiki.archlinux.org/index.php/Power_management
Documentation=http://forums.debian.net/viewtopic.php?f=5&t=129088
Documentation=https://wiki.debian.org/SystemdSuspendSedation
Conflicts=hibernate.target hybrid-sleep.target
Before=sleep.target
StopWhenUnneeded=true

[Service]
Type=oneshot
RemainAfterExit=yes
Environment="ALARM_SEC=10800"
Environment="WAKEALARM=/sys/class/rtc/rtc0/wakealarm"

ExecStart=/usr/sbin/rtcwake --seconds $ALARM_SEC --auto --mode no
ExecStop=/bin/sh -c '\
ALARM=$(cat $WAKEALARM); \
NOW=$(date +%%s); \
if [ -z "$ALARM" ] || [ "$NOW" -ge "$ALARM" ]; then \
  echo "suspend-sedation: Woke up - no alarm set. Hibernating..."; \
  systemctl hibernate; \
else \
  echo "suspend-sedation: Woke up before alarm - normal wakeup."; \
  /usr/sbin/rtcwake --auto --mode disable; \
fi \
'

[Install]
WantedBy=sleep.target
```

Then, just enable it:

```
% sudo systemctl enable suspend-sedation
```


## Pending issues

### 2019-05-15

I see restarts of the WiFi driver from time to time
(apparently, after periods of intense WiFi use).
The sympthom is continuous "ieee80211 phy0: Hardware reset was requested"
messages:

```
[ 1098.438897] ieee80211 phy0: Hardware restart was requested
[ 1102.911225] iwlwifi 0000:02:00.0: Error sending STATISTICS_CMD: time out after 2000ms.
[ 1102.911240] iwlwifi 0000:02:00.0: Current CMD queue read_ptr 93 write_ptr 94
[ 1102.911404] iwlwifi 0000:02:00.0: HW error, resetting before reading
[ 1102.918383] iwlwifi 0000:02:00.0: Start IWL Error Log Dump:
[ 1102.918408] iwlwifi 0000:02:00.0: Status: 0x00000100, count: -560800355
[ 1102.918421] iwlwifi 0000:02:00.0: Loaded firmware version: 38.755cfdd8.0
[ 1102.918434] iwlwifi 0000:02:00.0: 0x98CB9419 | ADVANCED_SYSASSERT
...
[ 1102.919062] ieee80211 phy0: Hardware restart was requested
...
```

In some cases, this leads to the WiFi driver stop working, and to kernel panics.

This seems to be a [known problem affecting many computers with the same WiFi driver](https://bugzilla.kernel.org/show_bug.cgi?id=201713), Wireless-AC 9260.

Apparently, it is fixed in kernel 4.20 (current one in testing is 4.19),
with firmware `-41.ucode` (current one for testing are `-41.ucode`
and `-38.ucode`, in package firmware-iwlwifi).

I think my only chance is, for now, to wait until 4.20 is in Debian...


#### Further notes on this issue

Maybe I'm having some other problem. The trace I get with dmesg is
maybe a bit different from what the issue mentioned above found:

```
[   77.233370] iwlwifi 0000:02:00.0: firmware: direct-loading firmware iwlwifi-9260-th-b0-jf-b0-38.ucode
[   77.234358] iwlwifi 0000:02:00.0: loaded firmware version 38.755cfdd8.0 op_mode iwlmvm
[   77.276269] Adding 16691196k swap on /dev/mapper/librito--vg-swap_1.  Priority:-2 extents:1 across:16691196k SSFS
[   77.287562] iwlwifi 0000:02:00.0: Detected Intel(R) Dual Band Wireless AC 9260, REV=0x324
[   77.336160] iwlwifi 0000:02:00.0: base HW address: 18:56:80:ee:67:22
...
[   83.325684] iwlwifi 0000:02:00.0: Unhandled alg: 0x707
...
[  262.735955] iwlwifi 0000:02:00.0: Error sending SCAN_CFG_CMD: time out after 2000ms.
[  262.735973] iwlwifi 0000:02:00.0: Current CMD queue read_ptr 194 write_ptr 195
[  262.736138] iwlwifi 0000:02:00.0: HW error, resetting before reading
[  262.743138] iwlwifi 0000:02:00.0: Start IWL Error Log Dump:
[  262.743154] iwlwifi 0000:02:00.0: Status: 0x00000100, count: -560800353
[  262.743162] iwlwifi 0000:02:00.0: Loaded firmware version: 38.755cfdd8.0
[  262.743171] iwlwifi 0000:02:00.0: 0x98CB941A | ADVANCED_SYSASSERT
...
[  262.743703] iwlwifi 0000:02:00.0: 0x48E61756 | isr status reg
[  262.743754] ieee80211 phy0: Hardware restart was requested
[  266.323698] iwlwifi 0000:02:00.0: Queue 0 is inactive on fifo 2 and stuck for 2500 ms. SW [113, 114] HW [162, 162] FH TRB=0x0a5a5a5a2
[  266.324963] iwlwifi 0000:02:00.0: Hardware error detected. Restarting.
[  266.325127] iwlwifi 0000:02:00.0: HW error, resetting before reading
[  266.332181] iwlwifi 0000:02:00.0: Start IWL Error Log Dump:
...
```

This seems to happen only when conneted to WiFi networks using the 5 GHz
band, so a temporary fix is to connect only to 2.4 GHz bands...
To know that the proble is not appearing, just run:

```
sudo dmesg | grep restart
```

If no new line appears, this problem is now showing up.

**Note from my future self:** Now, well above 4.20, I've never experienced these problems again.

## Improving microphone sound (removing some background noise)

The background noise I hear when speaking to the microphone can be removed (in part) by activating the ALSA echo cacelling module. I follow these [instructions](https://marcosmarti.org/como-eliminar-el-ruido-de-fondo-de-un-microfono-en-linux/).

First, edit `/etc/pulse/default.pa`, adding at the end of the file:

```
load-module module-echo-cancel source_name=noiseless
...
set-default-source noiseless
```

To activate the new configuration:

```
$ pulseaudio -k
```
