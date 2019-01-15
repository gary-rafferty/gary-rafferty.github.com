---
layout: single
title: Ruby client for the Perfect Audience API
categories: [software]
tags: [ruby gem]
---
I recently wrote a Ruby gem called [Perfecta](http://garyrafferty.com/perfecta), that wraps the Perfect Audience reporting API.

We use Perfect Audience for our retargeting campaigns and having the Ruby client makes it easy to integrate the reports and analytics into other systems.

Full documentation is over on [Github](https://github.com/gary-rafferty/perfecta) and the [project page](http://garyrafferty.com/perfecta), but here's a quick a quick overview 

```bash

gem install perfecta

# or place this into your Gemfile and bundle install

gem 'perfecta'

```

And then interacting with the API is as simple as

```ruby

client = Perfecta::Client.new do |c|
  c.email = 'email@ddress'
  c.password = 'password'
end

p client.campaign_reports(interval: 'yesterday').inspect

```

Check out the project page for more info and usage instructions
