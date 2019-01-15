---
layout: single
title: A ruby client for free tech books
categories: [software]
tags: [ruby gem]
---
If you're not already using [IT-Ebooks.info](http://it-ebooks.info/) for sourcing ebooks, then you probably should be.  

I hadn't realised that they also have a simple API interface..... Boom!

I wrote a [basic ruby client](https://github.com/gary-rafferty/itebooks) for accessing said API. It's quite basic, but does the trick. Feel free to [fork the repo](https://github.com/gary-rafferty/itebooks/fork) and make it better.

### Usage

Using the gem is really simple.

First off, grab it from Rubygems

```bash
gem install itebooks
```

Then in your app/irb, you can do the following

Search for some Ruby books.

```ruby

require 'itebooks'

# search the api 
books = Itebooks::Book.search('ruby')

```

Get more detailed infomation on a particular book

```ruby
require 'itebooks'

book = Itebooks::Book.find(2973000696)

p book.title # "Metaprogramming Ruby"
p book.sub_title # "Program Like the Ruby Pros"
p book.download # "http://filepi.com/i/a16hVNr" Download link (requires captcha completion)

p book.inspect # displays all attributes for a book
```

The `book.download` attribute is a direct link to a download of the book. This page does have a captcha though, so you'll need to go grab it manually.
