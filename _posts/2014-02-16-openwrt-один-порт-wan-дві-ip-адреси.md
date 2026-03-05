---
layout: post
title: "OpenWrt, один порт wan дві ip адреси"
date: 2014-02-16 19:32:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2014/02/openwrt-2-wan-ip.html
---

[![](/assets/images/blog/b095a28b2e412c71-3776724c90b53b7b.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjY7x6Rt_r3omUnm3D9SGqwcH8M5RLeJaRGcI9yM9V68Q-4fxuQqBmoUErTi0KgYOdA2Mihi-Axa1vUESkX-2He91T3gIF3XvEYvI8tGphpIa0AhBPLq9MwUepq08xZkBCdK1PANg86vw5U/s1600/openwrt1.png)

OpenWrt  
один wan два ip  
  
Для експериментів вирішив підключити дві IP адреси, основну і додаткову що надав провайдер для IPTV.  
  
  
/etc/config/network  
  

```
config interface 'wan'
        option _orig_ifname 'eth0.2'
        option _orig_bridge 'false'
        option ifname 'eth0.2'
        option proto 'static'
        option ipaddr '10.66.18.159'
        option netmask '255.255.255.0'
        option gateway '10.66.18.254'
        option dns ' 8.8.8.8'
        option metric '5'

config interface 'wan2'
        option proto 'static'
        option ifname 'eth0.2'
        option ipaddr '10.15.22.172'
        option netmask '255.255.255.128'
        option gateway '10.15.22.254'
        option metric '10'
```

  

```
# route
Kernel IP routing table
Destination     Gateway         Genmask        Flags Metric Ref     Use Iface
default         254.18.66.10    0.0.0.0        UG    5      0       0  eth0.2
default         254.22.15.10    0.0.0.0        UG    10     0       0  eth0.2
```

  

```
# traceroute -n 8.8.8.8

traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 38 byte packets

 1  10.66.18.254  0.589 ms  0.444 ms  0.808 ms
```
