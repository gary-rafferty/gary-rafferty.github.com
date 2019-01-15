---
layout: single
title: Using Node.JS, Express & MongoDB on Heroku
categories: [software]
tags: [nodejs,express,mongodb,heroku]
---
I've been playing with [Node.JS](http://nodejs.org) and [Express](http://expressjs.com) as of late, and earlier today I wanted to put a simple 
application that used MondogDB up on [Heroku](http://heroku.com).

I was using [Mongolian](https://github.com/marcello3d/node-mongolian) as the driver for Mongo so this is just a quicky post to scribble down the way 
I went about it all (so I don't need to go flicking through a load of docs next time)

__I'll do up a proper post on the whole thing at another stage__

## Heroku setup

```bash
cd /your/node/app
heroku create --stack cedar
echo "web: node web.js" > Procfile
echo "node_modules" > .gitignore
git init .
git add .
git commit -m "Initial commit"
git push heroku master
heroku config:add NODE_ENV=production
```

All fairly standard and ripped from the Heroku guides.

## MongoHQ setup

Next we need to add the MongoHQ addon to our Heroku app

```bash
$: heroku addons:add mongohq:free
```

This sets up a free database with MongoHQ and sets a heroku environment variable MONGOHQ_URL which points to the MongoDB instance.

## Mongolian configuration

Lastly, config Mongolian such that it will use a local MongoDB instance on your own machine, and the MongoHQ instance when on Heroku.

```javascript
var Mongolian = require('mongolian');
var server, db;

if(process.env.MONGOHQ_URL) {
  db = new Mongolian(process.env.MONGOHQ_URL);
} else {
  server = new Mongolian;
  db = server.db('your_db_name');
}
```

Fairly handy, but the last bit threw me a little as Mongolian returns the db handle when initialized with a connect string, but not if using default host and port.
