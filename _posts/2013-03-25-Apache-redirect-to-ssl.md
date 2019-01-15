---
layout: single
title: Apache redirect to SSL
categories: [software]
tags: [apache]
---
This is something I can never remember, and keep having to Google.

Use case is that I want to redirect all http:// trafic to https:// and maintain the original URL

```bash

RewriteEngine On
RewriteCond %{HTTPS} off
RewriteRule (.*) https://%{HTTP_HOST}%{REQUEST_URI}
```

Stick that into your Apache config or .htaccess and it should do the trick.
