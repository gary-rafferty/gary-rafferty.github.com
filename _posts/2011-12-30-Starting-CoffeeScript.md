---
layout: single
title: First steps with CoffeeScript
categories: [software]
tags: [coffeescript]
---
I've been intending to use CoffeeScript for ages now. Rails adopting it was the kick up the arse I needed to go and learn it.
I got a copy of [CoffeeScript](http://pragprog.com/book/tbcoffee/coffeescript) as a Christmas present from Orlaith and started reading it today.
First thing was to rewrite the well known Node.JS simple server in CoffeeScript. Hardly a massive task but good first steps to get a feel for the syntax etc.

Below is the CoffeeScript for the simple node server (nodeserver.coffee)

```coffeescript
http = require 'http'

http.createServer (req, res) ->
  res.writeHead 200, 'Content-Type': 'text/plain'
  res.end 'Hello, World!'
.listen 8000

console.log 'Server running at http://127.0.0.1:8000/'
```

And here is the compiled javascript from running coffee -c nodeserver.coffee

```javascript

(function() {
  var http;

  http = require('http');

  http.createServer(function(req, res) {
    res.writeHead(200, {
      'Content-Type': 'text/plain'
    });
    return res.end('Hello, World!');
  }).listen(8000);

  console.log('Server running at http://127.0.0.1:8000/');

}).call(this);
```

I know which version I prefer.
