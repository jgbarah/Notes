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
