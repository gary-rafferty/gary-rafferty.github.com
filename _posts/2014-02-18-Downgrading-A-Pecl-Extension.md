---
layout: single
title: Downgrading a PECL extension
categories: [software]
tags: [pecl]
---
I recently upgraded the Mongo PECL extension that a project depended upon, only to get hit with a load of Segfaults on OSX.  
I had used the `pecl upgrade mongo` command, but couldn't find a commmand to downgrade it.  

The solution was to use the -f flag to instruct PECL to install a particular version, in this case, 1.2.12

```bash
  # format: package-version
  sudo pecl install -f mongo-1.2.12
```

You can then run `pecl list` to verify that the correct version has been installed.
