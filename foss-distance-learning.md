# Free software for distance learning

[[Spanish](foss-distance-learning-es)]

These are some notes about free, open source software (FOSS) that could be useful when going remote, in this business of helping people in their learning process (aka, distance learning).

Conducting distance learning sessions, and producing materials for distance learning involve many different areas. This document tries to include tools for many different scenarios, but maybe the core one would be:

* Production of multimedia materials (screencasts, recordings of talks using slides, recordings of resolution of exercises, etc), including you talking while presenting different materials, usually on the screen of your computer. Some of these materials could be recorded and later uploaded to some video server (including Youtube, Twitch, or Vimeo), or streamed live.

* Editing those multimedia materials.

* Videoconferencing with students, maybe for regular lectures, for Q&A sessions, etc.

* Maintaining social contact with students as a group, usually through chat or messaging applications.

The current table of contents of this document is:

* [Videoconferencing](#videoconferencing)
* [Video streaming and recording](#videostreaming)
* [Video editing](#videoediting)
* [Audio edition](#audioediting)
* [Whiteboards and writing on screen](#whiteboard)
* [Chat groups](#chat)
* [Useful tricks](#tricks)
* [Everything together](#together)
* [Deployment and installation](#deployment)
* [Other useful resources](#resources)
* [License and contributions](#license)


## <a name="videoconferencing"></a>Videoconferencing


* [Jitsi](https://jitsi.org). Toolset for building videoconferencing systems. It can be used directly, just with a web browser ([Meet.jit.si](https://meet.jit.si). You don't need to open an account, just decide on a name for your room, and "start a new meeting". Grant permissions to your mic and webcam, share the link with your students, and you're ready to go. You can also use Jitsi from Riot (and other Matrix clients, see below), or from your own deployment (see deployment notes below).

[Usable in most browsers]


## <a name="videostreaming"></a>Video streaming and recording

* [OBS Studio](https://obsproject.com/), from Open Broadcaster Software, is a very powerful and complete application for broadcasting live, or for recording. It can screencast from your desktop, record from your webcam, or from other video and audio sources. You can use to stream live via, or to upload to, Youtube, Twitch, etc.

[Usable in Linux, Windows, MacOS]

* [Peek](https://github.com/phw/peek). Very easy recording of screencasts.

[Usable in Linux]

* [Kazam](https://launchpad.net/kazam). Simple screencasts, including audio.

[Usable in Linux]

* [SimpleScreenRecorder](https://www.maartenbaert.be/simplescreenrecorder/) is a program to record the desktop on Linux. It allows to select the screen to record (useful when using multiple monitors) or record whole screen or an specific region. It records video and audio and supports different formats.

[Usable in [Linux](https://www.maartenbaert.be/simplescreenrecorder/#download)]


## <a name="videoediting"></a>Video editing

* [VLC](https://vlc.media/) is mainly a media player, but is also has some editing capabilities and it can record the desktop activity ([How to record desktop](https://www.vlchelp.com/how-to-record-desktop/)). You can read about many of them in [A Full Guide to Use VLC as An Video Editor](https://videoconverter.wondershare.com/vlc/how-to-use-vlc-as-a-video-editor.html).

[Usable in Linux, Windows, MacOS, Android, iOS, etc.]

* [OpenShot](https://www.openshot.org/) is a relatively simple video editor, but good enough for many purposes. One of its main advantages is likely how easy to use it is.

[Usable in Linux, Windows, MacOS]

* [Kdenlive](https://kdenlive.org/) is a relatively advanced video editor, but rather straightforward to use.

[Usable in Linux, Windows]

* [Shotcut](https://shotcut.org/) is a relatively easy to use video editor, capable of most common tasks.

[Usable in Linux, Windows, MacOS]

* [Flowblade](https://jliljebl.github.io/flowblade/) is a multitrack non-linear video editor. Maybe more complex than other video editors, but maybe more capable as well.

[Usable in [Linux](https://jliljebl.github.io/flowblade/download.html)]

## <a name="audioediting"></a>Audio edition

* [Audacity](https://www.audacityteam.org/) is an easy-to-use, multi-track audio editor and recorder. Useful to clean the audio track of a class recording, or to produce a class-as-a-podcast, for example.

[Usable in [Linux](https://www.audacityteam.org/download/linux/), [Windows](https://www.audacityteam.org/download/windows/), [MacOS](https://www.audacityteam.org/download/mac/)]


## <a name="whiteboard"></a>Whiteboards and writing on screen

* [Openboard](https://github.com/OpenBoard-org/OpenBoard/wiki) is an interactive whiteboard application with some screencasting capabilities.

[Usable in [Linux, Windwos, MacOS](https://github.com/OpenBoard-org/OpenBoard/wiki/Downloads)]

* [Gromit-MPX](https://github.com/bk138/gromit-mpx) is a tool to annotate anything in the desktop. Draw lines or whatever on top of anything you may have on your desktop, using a pointer or a stylus on a tablet.

[Usable in Linux]


## <a name="chat"></a>Chat groups

* [Matrix](https://matrix.org/) is a standard for real time communication. With it you can establish messaging groups, in ways very similar to Whatsapp or Telegram, but in a federated way (you can decide your Matrix provider, but your messages will reach any other person using any other provider in the network). A popular application for using Matrix is [Riot.im](https://riot.im/), which you can use just with a web browser, or via a mobile app, or a desktop app. From Riot.im you can also start Jitsi sessions (videoconferencing with the people in a chat group).

[Usable in most browsers, Android, iOS, Linux

## <a name="tricks"></a>Useful tricks

* [How do I highlight my mouse pointer while screen recording?](https://askubuntu.com/questions/777896/how-do-i-highlight-my-mouse-pointer-while-screen-recording) (in Linux).

## <a name="together"></a>Everything together

Posts and tutorials on how to setup some of these tools together, in some cases in combination with other non-FOSS tools, or services:

* [Teaching on Twitch](http://matthematics.com/teach-on-twitch/), and [Teaching on Twitch II: My Rig](http://matthematics.com/teach-on-twitch-my-setup/) by Matt Salomone. Features, among other non-FOSS tools and services, OBS. Includes descriptions of the hardware setup used.

## <a name="deployment"></a>Deployment and installation

* Jitsi (the server side) can be deployed with relative easy in a Linux box (you have Debian and Ubuntu packages, for example), or from [Docker containers](https://github.com/jitsi/docker-jitsi-meet).

## <a name="resources"></a>Other useful resources

* [De-google-ify Internet](https://degooglisons-internet.org/en/list): list of services, most of them powered by free software, which could be use as alternatives to many Internet-based services. Maintained by [Framasoft](https://framasoft.org/en/association/). Most of the services could be easily deployed for an institution.

## <a name="license"></a>License and contributions

This document is distributed under the [Creative Commons Attribution-ShareAlike 4.0 International license](https://creativecommons.org/licenses/by-sa/4.0/) (CC BY-SA 4.0).

All the content in this document can be found in a [GitHub repository](https://github.com/jgbarah/Notes/).

If you want to contribute, please [open an issue with your comments, suggestions or contributions in this repository](https://github.com/jgbarah/Notes/issues/new), or better, [submit a pull request to it](https://github.com/jgbarah/Notes/pulls).

If you want to help me in translating this document into Spanish, let me know via a new issue (or a pull request with a part of the translation).
