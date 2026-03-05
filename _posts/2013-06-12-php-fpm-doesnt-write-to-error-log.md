---
layout: post
title: "PHP-FPM doesn't write to error log"
date: 2013-06-12 23:29:00 +0000
tags: ["nginx", "php"]
blogger_orig_link: https://lexxai.blogspot.com/2013/06/php-fpm-doesnt-write-to-error-log.html
---

![](/assets/images/blog/8f004e98e68fe595-839a8581e7a081ae.gif)

  
  
  
<http://stackoverflow.com/questions/8677493/php-fpm-doesnt-write-to-error-log>  
  
PHP-FPM.conf:  
  

```
; Redirect worker stdout and stderr into main error log. If not set, stdout and
; stderr will be redirected to /dev/null according to FastCGI specs.
; Default Value: no
catch_workers_output = yes
```
