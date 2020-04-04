# Life in testing

Living in Debian testing is awesome, but has its issues as well...

## Fixing Gnome keyring so that it doesn't provide ssh-agent functionality

When using my RSA key to ssh a remote machine, I got an error:

```
Agent admitted failure to sign using the key.
```

After looking around, I found a [very good description of my problem for Ubuntu](https://chrisjean.com/ubuntu-ssh-fix-for-agent-admitted-failure-to-sign-using-the-key/). Unfortunately, the answer provided there didn't work for me, because it involves using a graphical user interface to disable Gnome services on startup, which seems to be available only in Unity. The description includes a test to be sure what the problem is, and a good description of what is happening. The problem is related to gnome-keyring providing ssh-agent services, but not handling well RSA keys.

Fortunately, I fond a [page describing how to disable ssh-agent service for gnome-keyring](http://blog.josefsson.org/tag/keyring/). The fix is simple:

```
jas@latte:~$ mkdir ~/.config/autostart
jas@latte:~$ cp /etc/xdg/autostart/gnome-keyring-ssh.desktop ~/.config/autostart/
jas@latte:~$ echo 'Hidden=true' >> ~/.config/autostart/gnome-keyring-ssh.desktop
```

And after that, logout and login again. And done! The only trouble is that now I have to manually use ssh-add for ssh-agent to include the new keys, which are no longer stored in the Gnome keyring.

### Update on 2017-09-27

I upgraded my testing box to buster, and ssh-add stopped working.
After some tinkering,
it seems that is due to a certain combination of GNOME using Wayland,
which triggers a kind of a bug.
The issue can be fixed with some systemd user scripts,
which were a bit tricky until I got nailed down.

The problem is that `ssh-add` (nor `ssh`) does not find `ssh-agent`.
The reason is that the shell variable `SSH_AUTH_SOCK` has the wrong
value, `/run/user/[uid]/keyring/ssh`,
apparently set by `gnome-keyring`,
although it is not creating it (because it is disabled, see above).

The fix is described in
[this answer to "Where does gnome-keyring set $SSH_AUTH_SOCK?"](https://unix.stackexchange.com/a/360309/119113)
(the problem is described in that same question):

> Gnome-Session has a hardcoded override for `SSH_AUTH_SOCK`
under wayland for some reason.
See the [following commit](https://github.com/GNOME/gnome-session/commit/a8896ccad65583885735a04205351f48a42f29ae).
>
> The workaround? Set an environment variable to disable this behavior: `GSM_SKIP_SSH_AGENT_WORKAROUND=1`. This short-circuits the environment setting code.

It is important to notice that the fix to
`~/.config/autostart/gnome-keyring-ssh.desktop`, described above,
is still needed.

For implementing the workaround, I had to learn a bit of systemd user scripts.
Making a long story short, I had to:

* Create the `.config/systemd/user` directory, for systemd user scripts.

* In it, create the file `ssh-agent.service`,
with the following content:

```
[Unit]
Description=SSH Agent
IgnoreOnIsolate=true

[Service]
Type=forking
Environment=SSH_AUTH_SOCK=%t/ssh-agent.socket
ExecStart=/usr/bin/ssh-agent -a $SSH_AUTH_SOCK
ExecStartPost=/bin/bash -c "/bin/systemctl --user set-environment SSH_AUTH_SOCK=$SSH_AUTH_SOCK GSM_SKIP_SSH_AGENT_WORKAROUND=1"

[Install]
WantedBy=default.target
```

To test it, you can just start it manually:

```bash
$ systemctl --user start ssh-agent
```

That sets the right `SSH_AUTH_SOCK`, but in its own shell,
so to test it you need to manually set the right value for it:

```bash
$ SSH_AUTH_SOCK=/run/user/1000/ssh-agent.socket ssh-add
```

If you are asked for your passwd for `ssh-add`, you're in the right way.
But I was still not done.
It is not enough to create the above file.
For the new service to be started upon user login,
I still needed to "enable" the service:

```bash
$ systemctl --user enable ssh-agent
```

That creates a new directory, `.config/systemd/default.target.wants`,
and a link in it, `ssh-agent.service`,
pointing to the above file.

And now, that's it. Log out, log in, and I could happily run
and use `ssh-add` and then `ssh`.


## Stopping automatic download of software updates

2015-08-08

It seems my laptop is downloading software updates periodically. This is handy, because when I want to update the software packages, they are already there. But it is a problem when I'm connected through low bancwidth networks. So I decide to disable that. The main problem was to know whcih application was doing the updates. After considering some candidates, including packagekit and some of its related packages, it seems it is gnome-software. To disable in it, you have to tinker a bit with dconf-editor. No rocket science, just set to False (unckeck, in the graphical interface) the property "download-updates" in org.gnome.software.

## Installing Skype (and enabling multiarch)

The Skype I download from skype.com seems to be for i386 (32 arch, not 64 arch, which is the arch I have in my laptop). I follow instructions in the [Debian Wiki for Skype](https://wiki.debian.org/skype). I enable multiarch, and then install the regular Skype for Linux (the 5th step is for fixing missing dependencies, if any, the 6th is for finishing skype configuration, but I'm not sure it's needed).

```
dpkg --add-architecture i386
apt-get update
wget -O skype-install.deb http://www.skype.com/go/getskype-linux-deb
dpkg -i skype-install.deb
apt-get -f install
dpkg -i skype-install.deb
```
## Installing add-apt-repository

This is a convenient tool to manage apt repositories. In fact, I installed the gtk version, which intalls as a dependency the command line tool:

```
apt-get install software-properties-gtk
```

That's it...

## Problems with access to screen by root under Wayland

It seems Wayland applications don't have access to the X11 auth methods.
The diagnosis for this problem is for example clear in the case of synaptic:

```bash
$ synaptic-pkexec
No protocol specified
Unable to init server: Could not connect: Connection refused

(synaptic:5209): Gtk-WARNING **: cannot open display: :0
```

To fix it, of the several solutions that are proposed out there,
I've decided to use one that allows me to follow on with
GNOME Wayland (instead of reverting to GNOME X11).
It is based on adding a description for an autostart application:
create a file `.config/autostart/xhost.desktop`:

```
[Desktop Entry]
Type=Application
Exec=/usr/bin/xhost +si:localuser:root
Hidden=false
NoDisplay=true
X-GNOME-Autostart-enabled=true
Name[en]=Xhost for local root
Name=Xhost for local root
```

Done... You need to log out and log in back to test it.


## Installing from unstable

From time to time, I find a package that I need, which is not in testing, but is in unstable (sid). If I want to give it a try, all I need is to include a line for sid in my /etc/apt/sources.list file:

```
deb http://deb.debian.org/debian/ sid main non-free contrib
```

Then, run:

```
$ sudo apt-get update
```

And now, check if the package is in sid (or in other distros). For example:

```
$ sudo apt-cache policy printrun
printrun:
  Installed: (none)
  Candidate: 2.0.0~rc5-1
  Version table:
     2.0.0~rc5-1 500
        500 http://deb.debian.org/debian sid/main amd64 Package
```

Then, I can install it, **with the needed dependencies from sid**:

```
$ sudo apt-get -t sid install printrun
```

...or install only the package from sid, but with dependencies from testing:


```
$ sudo apt-get install printrun/unstable
```

