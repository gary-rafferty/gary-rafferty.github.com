---
layout: single
title: Test-driven provisioning with Chef
categories: [software]
tags: [chef,tdd,inspec]
---

Chef is a killer tool for automating and testing your software stack.
In this post I'd like to demonstrate our method of TDD'ing our infrastructure.

Our goal will be an installation of Ruby atop Ubuntu. Start simple and progress
from there.

### Required tools
* Chef et al (Berkshelf, Kitchen)
* Vagrant

Visit the ChefDK installation [page](https://docs.chef.io/install_dk.html) for
detailed information on how to get up and running. ChefDK will install chef and
a host of related tools (Cookstyle, Kitchen etc).

### Overview
The process should be familiar for those of you who practice TDD.

I also assume a basic understanding of Chef (recipes, cookbooks, roles etc).

1. We will define a failing spec (we will be using `Inspec`) that will check for a valid
Ruby installation.
2. We will implement a recipe to install Ruby
3. We will rerun our previous failing spec and assert that it is now in the
green.

I prefer to keep the recipes atomic and fulfilling a single requirement.
Smaller recipes can be grouped together into larger recipes, or ran sequentially
using a runlist within your Chef role.

#### Kitchen setup

Within your cookbook, add or modify your `.kitchen.yml` file to be something
along the lines of the following.

```yaml
---
driver:
  name: vagrant

provisioner:
  name: chef_solo
  always_update_cookbooks: true

verifier:
  name: inspec

platforms:
  - name: ubuntu/xenial64

suites:
  - name: ruby
    run_list:
      - recipe[mycookbook::ruby]
    verifier:
      inspec_tests:
        - test/ruby_spec.rb
```

There's a few things going on here, but at a high level, we are telling Kitchen
that we intend to run out tests against a Vagrant instance. That we will be
using chef-solo as the provisioner (ideal for local development), and that we will be using a basic Vagrant image of ubuntu.

Next we define the suites. We have added a singe suite which will run one recipe
and then run one test against that recipe.

#### Writing our failing spec

`test/ruby_spec.rb`

```ruby
control 'RUBY 2.3 AGENT/ruby runtime' do
  title 'Installs ruby 2.3'

  describe package('ruby2.3-dev') do
    it { should be_installed }
  end

  describe bash('ruby -v') do
    its('stdout') { should include 'ruby 2.3' }
  end
end
```

This is our failing spec. It will continue to fail until we implement the recipe
below.

#### Implementing the recipe to install Ruby

Installing Ruby is made easy by using the Brightbox cookbook. Simply including
the default recipe and setting the versions in your attributes file are the only
steps that are required.

`recipes/ruby.rb`

```ruby
include_recipe 'brightbox-ruby::default'
```

`attributes/default.rb`

```ruby
default['brightbox-ruby']['gems']                = %w(rake bundler)
default['brightbox-ruby']['install_ruby_switch'] = true
default['brightbox-ruby']['default_action']      = :install
default['brightbox-ruby']['version']             = '2.3'
```
#### Asserting success

Run the suite using
```bash
chef exec kitchen test ruby # where ruby matches the suite name
```

And you should now have a passing spec.

The main takeaway from this article should be that there are benefits
to creating small recipes that perform a single task. If and when something goes
wrong, your CI build will be able to point to the exact spec that has failed. If
each spec corresponds to a recipe, then you will easily know where to start
debugging.
