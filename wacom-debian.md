# Wacom Intuos CTL-4100 in Debian

I bought a Wacom Intuos CTL-4100 tablet.
It connects via USB with my laptop. When I connected, it somehow worked,
but a bit erratically. Also, GNOME Settings (Wacom Tables) could not
find it (neither the table nor the stylus).

Fortunately, I found exactly the problem I had, when I noticed the
LED on the table was dim, instead of bright. It seems the tablet
was in "Android mode", whcih sends different commands.
To change it back to "desktop mode", I only had to press the leftmost
and rightmost buttons in the table for about 3 seconds.
I noticed the light changed to bright, and now GNOME Settings
perfectly found it, and it was no longer erratic.

There is a detailed description of the problem in
"[Issue: My Wacom Intuos (CTL-4100 or CTL-6100) has a dim status LED and behaves erratically](https://github.com/linuxwacom/xf86-input-wacom/wiki/Known-Issues#android-misdetect)".