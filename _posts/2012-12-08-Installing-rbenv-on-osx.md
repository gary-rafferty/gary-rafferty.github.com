---
layout: single
title: Installing rbenv on OSX / ZSH
categories: [software]
tags: [rbenv]
---
I recently switched from using rvm to using rbenv for managing different ruby versions.
The install is relatively straightforward, but a few caveats when using ZSH.

```bash
brew update

brew install rbenv

rbenv install 1.9.3-p327

ruby --version # should show the installed version

which ruby # should point to ~/.rbenv/shims/ruby
```

In the README, it states to add some statements to your bash_profile, but if you are using ZSH, to put it into .zshenv, this however did not work for me, so instead, stick the following into your .zshrc

```bash
if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi
```

Be sure that `$HOME/.rbenv/bin` is prepended to your `$PATH`

Simple as.
