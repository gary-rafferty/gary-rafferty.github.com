---
layout: single
title: li3_airbrake - Lithium Airbrake plugin
categories: [software]
tags: [php lithium]
---
I recently wrote a [plugin](http://www.lithium101.com/plugins/gary-rafferty/li3_airbrake) for Lithium that reports application errors to the awesome [Airbrake.io](https://airbrake.io) service.
I've been writing code for [various](https://github.com/airbrake/airbrake-php) [Airbrake](https://github.com/airbrake/airbrake) projects and gems for the last year or so and it really is a great error tracking tool.
This plugin simply allows you to easily use Airbrake within your Lithium application.

It's in its' early stages, and could be a little rough around the edges, but it provides the core functionality of catching errors as they
occur in your application, parsing them, extracting the backtrace and other important variables, and sends them over to Airbrake.

For now, it can be installed by cloning down the git repository into your project's app/libraries directory and hooking it into the bootstrap process.

## Installation

Clone into the libraries directory

```bash
git clone git://github.com/gary-rafferty/li3_airbrake.git app/libraries/li3_airbrake
```

## Bootstrap

```php
<?php
  Libraries::add('li3_airbrake', array('apikey' => '[AIRBRAKE-API-KEY]'));
?>
```

By doing so, li3_airbrake will catch any application errors and report them to Airbrake for aggregation and notification.

There's still a few features that I want to add, such as filtering out common errors and blacklisting variables.

Feel free to [contribute to the project on GitHub](https://github.com/gary-rafferty/li3_airbrake).
