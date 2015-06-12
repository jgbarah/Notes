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
