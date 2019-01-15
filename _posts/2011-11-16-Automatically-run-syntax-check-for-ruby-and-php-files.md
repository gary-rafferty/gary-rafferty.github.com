---
layout: single
title: Automatically check PHP / Ruby syntax on file save
categories: [software]
tags: [ruby,php]
---
# {{ page.title }}

One of the wicked things about VIm is that you learn a new trick every day; well, I do anyway.

I program day to day in either PHP or Ruby, and I normally run a quick syntax check when I save a file.
Previously, I would save the file and run the following from the terminal

For PHP

```bash
php -l filename.php
```

For Ruby

```bash
ruby -cw filename.rb
```

Then I copped that I could run it without leaving VIm by entering the follwowing commands

For PHP

```bash
  :! php -l %
```

For Ruby
```bash
  :! ruby -cw %
```

So the next nicety was to have VIm run the check automatically when I save either a PHP or Ruby file.

Simple pop the following in your vimrc and away you go.

```bash
au BufWritePost *.php !php -l %
au BufWritePost *.rb !ruby -cw %
```

This simple instructs VIM that when saving a file with either extension, run the required command.
Dead handy,.... and you can swap out the extension and command required for your language of choice.
Enjoy,
