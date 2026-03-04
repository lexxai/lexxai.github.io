---
layout: post
title: "Додаткові імена для мережевих VLAN інтерфейсів у FreeBSD"
date: 2013-06-07 20:49:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2013/06/vlan-freebsd.html
---

![](/assets/images/blog/3b5d456ae2e09d37-d6724a32ac0eef39.jpg)

Дуже сподобалося як можна іменувати віртуальні інтерфейси у FreeBSD:
  

```
ifconfig_em0="inet 192.168.x.x netmask 255.255.255.192 descr LAN"
ifconfig_em0_ipv6="inet6 2002:d58d:87xx:1::1/64"
```

```
vlans_em0="wifi wifio" # vlan(4) interfaces
create_args_wifi="vlan 2"
create_args_wifio="vlan 3"
```

```
# WiFi Protected
ifconfig_wifi="inet 192.168.x.x netmask 255.255.255.240 mtu 1500 descr WiFi.Protected"
ifconfig_wifi_ipv6="inet6 2002:d58d:87xx:2::1/64"
```

```
# WiFi free
ifconfig_wifio="inet 192.168.x.x netmask 255.255.255.240 mtu 1500 descr WiFi.Open"
```

  
<http://www.hotplug.ru/2012/01/nastrojki-ip-i-ipv6-v-freebsd-9-0/>
