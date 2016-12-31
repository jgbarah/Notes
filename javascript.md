# Very personal notes on JavaScript-related stuff

## Installing globally with npm, but as a user

My Debian has its own nodejs, npm and other stuff. But at some point,
I want to use npm to install its own stuff, but not as root (mainly,
not to mangle with Debian packages). So, I want to install globally
with npm, but as my user, not root.

Fortuuntately, there is a nice recipe: [nstall npm packages globally without sudo on macOS and Linux](https://github.com/sindresorhus/guides/blob/master/npm-global-without-sudo.md). I adapted it slightly:

```bash
mkdir ~/.npm-packages
```

In ~/.npmrc:

```
prefix=~/.npm-packages
```

In ~/.bashrc:

```
# Ensure npm will find installed binaries and man pages
NPM_PACKAGES=~/.npm-packages
PATH=$NPM_PACKAGES/bin:$PATH
export MANPATH=$NPM_PACKAGES/share/man:$MANPATH
```

And that's it. Now, when I install with `npm install -g`, stuff gets installed in `.npm-packages`