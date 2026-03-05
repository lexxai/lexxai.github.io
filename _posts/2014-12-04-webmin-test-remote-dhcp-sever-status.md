---
layout: post
title: "Webmin test remote DHCP sever status"
date: 2014-12-04 23:17:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2014/12/webmin-test-remote-dhcp-sever-status.html
---

[![](/assets/images/blog/9ddc96bbb325c449-14f4d591fd681586.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgBrt5daM7TS6FHB7HVX2m0dVBJn8JV96dlotT130mS295kqxABkjg2q9Dj5szquWuCz-cOcEotMxAmgGVhfz7hanuHv6xaZXd8lMU1GZK8qqiMpKuyR7GLvQNMFxjFN5ErNpM00sM-70yG/s1600/wembindhcp.PNG)  
*Webmin, remote DHCP monitoring*

```
 nmap -sU -p 67-68 10.0.0.2 -Pn | grep open | wc -l | grep 2
```

  
  
Як це працює ?   
  

```
Starting Nmap 5.61TEST5 ( http://nmap.org ) at 2014-12-05 01:05 EET
Nmap scan report for server (10.0.0.2)
Host is up.
rDNS record for 10.0.0.2: server
PORT   STATE         SERVICE
67/udp open|filtered dhcps
68/udp open|filtered dhcpc
```
