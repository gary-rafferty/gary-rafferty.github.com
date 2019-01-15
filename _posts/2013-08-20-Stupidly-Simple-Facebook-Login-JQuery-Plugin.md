---
layout: single
title: Stupidly simple jQuery plugin for Facebook login
categories: [software]
tags: [ruby gem]
---
Using Facebook to authenticate users for you application is nice for a number of reasons.

* Reduces friction within the registration process.
* No need for your users to create and remember another login/password combination.
* If the user is already signed into Facebook, the registration is a one-click process.
* You can easily interact with the user's Facebook account.

There are 2 popular methodologies of implementing Facebook login, the first of which is server-side, and the second is client-side.
I generally opt to use the client-side approach as I feel it makes for a better user experience.

I've implemented this on roughly 10 - 20 applications over the last couple of months, and the code is generally the same.

Rather than the usual copying and pasting from one app to another, I've bundled the code into a [jQuery plugin](http://garyrafferty.com/flogin).

The plugin allows you to specify 

* your facebook appId
* the path to which the authenticated user data will be POSTed
* a callback onSuccess
* a callback onError
* a list of permissions that you require

The only mandatory argument is the appId, and sensible defaults are used for the rest.

See the [Github](https://github.com/gary-rafferty/flogin) page for more information and sample usage, but essentially, usage is as follows

```html
<!DOCTYPE html>
<html>
  <head>
  	<script type='text/javascript' src='jquery.js'></script>
  	<script type='text/javascript' src='flogin.js'></script>
  	<script type='text/javascript'>
  	  $(function() {
  	    $('#login').facebook_login({
  	      appId: 'YOUR-FACEBOOK-APP-ID',  # your facebook application id
  	      endpoint: '/sessions/new',      # where to POST the response to
  	      onSuccess: function(data) {},   # what to do on success, usually redirect
  	      onError: function(data) {}      # what to do on error
  	      permissions: 'read_stream'      # what permissions you need, default is just email
  	    });
  	  });
  	</script>
  </head>
  <body>
    <a href='#' id='login'>Login with Facebook</a>
  </body>
</html>
```

The function `facebook_login()` is invoked on a jQuery element's click event, and the rest is taken care of.
In the above example, the users is greeted with the Facebook authentication dialog, and permissions is requested for the user's email address.
When the user has authorized your application, a POST request is sent to /sessions/new with the user_id, email and access token.
Thereafter, the onSuccess() callback is invoked (you will likely redirect the user).
