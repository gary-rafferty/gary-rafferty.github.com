---
layout: single
title: CloudFormation and StackMaster (part 2)
categories: [infrastructure]
tags: [aws,cloudformation,stackmaster]
---

In [part one]({% post_url 2019-08-11-CloudFormation %}) of the series, we looked at using CloudFormation to codify your AWS infrastructure.
I concluded with mentioning some painpoints that you might encounter.
Here at Lonely Planet, we are using an open source tool called [StackMaster](https://github.com/envato/stack_master) to alleviate some of those painpoints.

Let's take a look at two of its killer features. Stack previews, and Parameter resolvers.

## Stack update previews

When you are updating your stack, StackMaster will provide you with a template
diff, and also, it will show you the actions that will be taken by CloudFormation.

Some CloudFormation actions will modify existing resources, but others require
the replacement of a resource with a completely new one. Previously, it was
necessary to consult the documentation for each resource (and dependent
resources) and assert how the stack will behave, but now, using StackMaster, you
are given a very clear indication of the proposed changes, and you can choose to
continue, or to stop the update.

If you choose to continue, the event stream is displayed during the operation.

Take a look at the following demo, taken directly from the project repository.

![StackMaster update demo](https://github.com/envato/stack_master/raw/master/apply_demo.gif?raw=true)

No more guesswork when applying updates to your infrastructure.

## Parameter resolvers

Parameter resolvers are probably my favourite feature of StackMaster, purely
because it not only allowed us to remove a great deal of code that queried the AWS API
for various resource identifiers, but it also enables cleaner reusability of
configuration across environments (one shared configuration but parameters for
all environment specific properties like hostnames).

Out of the box, StackMaster comes with plenty of resolvers, but you can also
write your own. Let's look at some of the ones that are available immediately
that we have found particularly helpful.

* Latest AMI by tag - enables you to dynamically source AMIs based on tagged
  values.
* Latest container from repository - enables you to retrieve your latest Docker
  image from ECR.
* ACM Certificates - easily look up certs by name.

There are whole host of other resolvers covering subjects like password lookup,
environment variables, and ejson.

## Write less code and do more

Using StackMaster has allowed us to remove a bunch of supporting code.
Previously, we would build the templates dynamically based on application and
environment, but now, we can define a single template per application, and then
use parameters and resolvers to define the template at runtime.

A number of our stacks are large, with complex dependency chains defined within.
Rolling out updates used to be a laborious, hands-on effort, but now,
utilizing StackMaster, it's not uncommon for us to run multiple updates in a single
day. Having the confidence to roll out updates across your infrastructure
enables you to progress rapidly and to maintain a healthy, forward moving
infrastructure layer.

10/10 would recommend :)
