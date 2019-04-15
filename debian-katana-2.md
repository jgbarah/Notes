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

## Suspension problem

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

This seems to be a [known problem affecting many computers with the same WiFi driver](https://bugzilla.kernel.org/show_bug.cgi?id=201713), Wireless-AC 9260.

Apparently, it is fixed in kernel 4.20 (current one in testing is 4.19),
with firmware `-41.ucode` (current one for testing are `-41.ucode`
and `-38.ucode`, in package firmware-iwlwifi).

I think my only chance is, for now, to wait until 4.20 is in Debian...

