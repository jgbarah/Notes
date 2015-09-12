# Git

## Using meld as git difftool

Following the advice in [Git Tip of the Day – viewing diffs graphically with meld](https://kparal.wordpress.com/2015/09/10/git-tip-of-the-day-viewing-diffs-graphically-with-meld/)
```
$ git config --global diff.tool meld
$ git config --global alias.dt 'difftool -d'
```

Meld is in Debian testing, so I just have to install it... and enable syntax highlighting, for more comfort (in Meld preferences).

Then, you can view differences easily:

```
$ git dt master..e84744de8949f20

```

# Atom

## Setting fonts for HiDPI

I already have Gnome scaled for my HiDPI screen. Atom 1.0.11, as installed, shows a reasonable font for text in the editor, and for menus (a bit small, but well, readable enough). But for example, for Settings, the font is way too small. To fix that, it is enough to include the following lines in .atom/styles.less:

```
html {
  zoom: 2;
}
```

After this, Settings are displayed in a reasonable font. But text in the editor is too large. Therefore, in Settings, I just change the default font to 18 (instead of 34, which was what I saw there).
