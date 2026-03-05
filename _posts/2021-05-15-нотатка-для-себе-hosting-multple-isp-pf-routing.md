---
layout: post
title: "Нотатка для себе. Hosting, Multple ISP, pf routing"
date: 2021-05-15 21:50:00 +0000
tags: ["FreeBSD", "ip", "ipv4", "ipv6
multiple", "pf", "route"]
blogger_orig_link: https://lexxai.blogspot.com/2021/05/hosting-multple-isp-pf-routing.html
---

[![](/assets/images/blog/a7ab05c5f0d6354c-13416996db463b47.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiIOwT1bIKIn2q1kbK8Y5RBQCs0jFyWMVyFdX1XUzYnnIeTNOH7EFI_r_PVEEZHGWixFMPo6_QTzDOFfmpciW5ZGi6j84aC6SD7bs_NmWGEFVDBypFL4T0cLlpZyCZZRW_bxYHrvnoDoMGk/s678/pftop.PNG)

Нотатка є віртуальна машина з FreeBSD v13, та двома інтерфейсами для vtnet0 (ISP1) так vtnet1 (ISP2).

Роль сервера - хостинг. Але ISP2 має швидкість 10Gbit/s, тому треба певні сайти віддавати через ISP1, інші через ISP2 з метою оптимізації.

rc.conf

```
#ISP1

ifconfig_vtnet0="inet 10.110.6.6  netmask 255.255.255.0"  
defaultrouter="10.110.6.1"  
  
ifconfig_vtnet0_ipv6="inet6 2105:1c00:7:1::6 prefixlen 64"  
ipv6_defaultrouter="2105:1c00:7:1::1"

#ISP2

ifconfig_vtnet1="inet 10.220.6.97 netmask 255.255.255.0"  
ifconfig_vtnet1_ipv6="inet6 2001:10:10:10::97 prefixlen 64"
```

За замовчуванням трафік іде через vtnet0 (ISP1).  
Для організації
відповіді для запиту з інтерфейсу vtnet1 не через шлюз vtnet0, а через
шлюз vtnet1 створено  правило для firewall pf.

pf.conf

```
ext_if1 = vtnet0
ext_gw1 = 10.110.6.1

ext_if2 = "vtnet1"
ext_gw2 = "10.220.6.1"
ext_gw2v6 = "2001:10:10:10::1"

#ipv4 reply-to
pass in on $ext_if2 reply-to ($ext_if2 $ext_gw2) inet from any to $ext_if2
  
#ipv6 reply-to
pass in   reply-to ($ext_if2 $ext_gw2v6) inet6 from !$ext_if2:network to $ext_if2.
pass out  route-to ($ext_if2 $ext_gw2v6) inet6 from $ext_if2 to !$ext_if2:network
```
