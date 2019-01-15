---
layout: single
title: Dynamically set li3 environment based on subdomain
categories: [software]
tags: [php lithium]
---
I've mentioned before that our latest project in work, is built on [Lithium](http://lithify.me).
It aint no Rails, but its not bad for a PHP framework! ;)

Today I needed to dynamically set the Environment based on the HTTP_HOST, specifically, development environment when a particular subdomain was used.

By default, the lithium detector will only set the environment to development if the request is coming from 127.0.0.1.

A quick and easy way to get around this, is to modify `/config/bootstrap/action.php` to check the HTTP_HOST and set the environment if a conditional is fulfilled.

```php
<?php
Dispatcher::applyFilter('run', function($self, $params, $chain) {

  /*
   * Check if the request is coming from the
   * particular subdomains, and set the ENV if so
   */
  $subs = array(
    'sub1.domain.com',
    'sub2.domain.com'
  );

  $requesting_host = $params['request']->env('HTTP_HOST');

  if(in_array($requesting_host,$subs)) {
    Environment::set('development');
  } else {
    Environment::set($params['request']);
  }

  // ... rest of filter definition ...

  return $chain->next($self, $params, $chain);
});
?>
```

There's probably a better way to do this, by setting an Apache env variable and checking that, but this was a quick and dirty hack that did the trick.
