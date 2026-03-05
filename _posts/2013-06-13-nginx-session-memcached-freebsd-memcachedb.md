---
layout: post
title: "Nginx, Session, memcached, FreeBSD, memcachedb"
date: 2013-06-13 22:50:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2013/06/ngnix-session-memcached-freebsd.html
---

![](/assets/images/blog/999b7a911302c7f6-d2a36ca103f4ab78.jpeg)

```
#cd /usr/ports/databases/memcachedb
#make install clean
```

rc.conf:  

```
memcachedb_enable="YES"
```

```
#/usr/local/etc/rc.d/memcachedb  start
```

```
#sockstat | grep 11211
nobody   memcachedb 70265 15 tcp4   127.0.0.1:11211       *:*
nobody   memcachedb 70265 16 udp4   127.0.0.1:11211       *:*
```

```
#cd  /usr/ports/databases/pecl-memcached  
#make install clean
```

```
#grep memcached  /usr/local/etc/php/extensions.ini 
extension=memcached.so
```

```
#/usr/local/etc/rc.d/php-fpm restart
```

/usr/local/etc/nginx/fastcgi\_params:  

```
fastcgi_param PHP_VALUE "session.save_handler=memcached\nsession.save_path=127.0.0.1:11211 enabled";
```

or php.ini:  

```
session.save_handler =  memcached
session.save_path=127.0.0.1:11211 enabled
```

```
#/usr/local/etc/rc.d/nginx restart
```

check phpinfo():  

|  |  |  |
| --- | --- | --- |
| session.save\_handler | memcached | memcached |
| session.save\_path | 127.0.0.1:11211 enabled | 127.0.0.1:11211 enabled |

  

```
#memstat --servers localhost
Server: localhost (11211)
         pid: 70265
         uptime: 2053
         time: 1371163073
         version: 1.2.0
         pointer_size: 64
         rusage_user: 0.094042
         rusage_system: 0.081473
         ibuffer_size: 512
         curr_connections: 5
         total_connections: 107
         connection_structures: 7
         cmd_get: 86
         cmd_set: 181
         get_hits: 85
         get_misses: 1
         bytes_read: 33199
         bytes_written: 25535
         threads: 4
```

  
  
  
Посилання:  

* [Зберігання сесій PHP у memcached](http://lagman.su/2009/12/php-sessions-memcache/)
* [Особливості зберігання сесій PHP у memcached](http://habrahabr.ru/post/38261/)
* [Memcache(d) для зберігання сесій PHP](http://beeznest.wordpress.com/2011/04/18/memcached-to-store-php-sessions/)
* [phpMemCacheAdmin - Memcached сервер admin у php для моніторингу та тестування](https://code.google.com/p/phpmemcacheadmin/wiki/InstallationGuide)
