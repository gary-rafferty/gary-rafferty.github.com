---
layout: single
title: Supply arguments to a rake task
categories: [software]
tags: [ruby rake]
---
Whilst working on a recent [project](https://github.com/gary-rafferty/gitline), I wanted to create a rake task that would send a curl request to a 
particular url. It was a pain to keep changing the resource id, so I decided to write a rake task that could accept the resource id as an argument.

For anyone looking to do something similar, it's actually quite simple.

```ruby

desc 'Send a sample payload to a repository (by _id)'
task :payload, :repo do |t,args|
  repo = args[:repo]
  sh "curl --data-urlencode payload@resources/sample_payload.json localhost:3000/hooks/#{repo}/new"
end
```

The above task, when invoked, simply POSTS the contents of the file 'sample_payload.json' to the given url.

It is called like this, where `50fdbac236974c8983000001` is the argument to be supplied; in my case, the _id of a MongoDB row.

```bash
# rake payload[REPO_ID]

rake payload[50fdbac236974c8983000001]
```

One little gotcha for ZSH users is that it must be quoted, like this

```bash
rake 'payload[50fdbac236974c8983000001]'
```

