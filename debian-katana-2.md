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
