---
layout: single
title: Some VIm tips for navigation
categories: [software]
tags: [vim]
---
VIm is my primary editor,.. actually, scratch that, my only editor.
I spend most of my awake time in terminals writing code and over the years I've found VIm to be one pretty powerful tool.

### Splits

One thing that I've always found especially useful for development is the ability to split the screen to view / edit multiple files at once.

To split the window vertically, simply use the command

{% highlight bash %}
:vsplit or :vs
{% endhighlight %}

and to split the window horizontally, use the command 

{% highlight bash %}
:split or :sp
{% endhighlight %}

You can yank and paste text between the spits and navigate around using <ctrl>+ww.

### Tabs

Another thing I use when writing code is VImTabs.

The four commands you need to remember are: 

To create a new tab

{% highlight bash %}
:tabnew
{% endhighlight %}

To close a tab

{% highlight bash %}
:tabc
{% endhighlight %}

To navigate to the next tab (right)

{% highlight bash %}
:tabn
{% endhighlight %}

To navigate to the previous tab (left)

{% highlight bash %}
:tabc
{% endhighlight %}

### Tab mappings

But having to type those command each time you want to navigate becomes a pain so here's a mapping that I use to speed things up.

{% highlight bash %}
"Mappings for tab controls
map <C-up> :tabnew<CR>
map <C-down> :tabc<CR>
map <C-left> :tabp<CR>
map <C-right> :tabn<CR>
{% endhighlight %}

These map ctrl+up to open a new tab, ctrl+down to close the current tab, ctrl+left to navigate left, and ctrl+right to navigate right.

Just stick these in your vimrc file and reload VIm.
