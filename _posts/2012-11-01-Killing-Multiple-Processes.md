---
layout: single
title: Killing multiple process instances from the terminal
categories: [software]
tags: [linux bash]
---
I sometimes have multiple instances of a process running, typically, worker scripts that are dealing
with clearing out a backlog. From time to time, I'll need to kill these scripts for one reason or another
and it's a pain in the swiss to copy and paste the ids of each process and kill individually.
Below is the command I use to kill all running instances of `myscript.rb`.


```bash
ps -deaf | grep -v grep | grep myscript.rb | awk '{ print $2 }' | xargs sudo kill -9
```

Essentially, this just greps through the process list for all processes with the name myscript.rb, 
cuts out their process ids and pipes them to the kill command. The grep -v grep command simply excludes
the grep search from the list of process ids.
