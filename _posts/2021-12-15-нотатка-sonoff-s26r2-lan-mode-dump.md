---
layout: post
title: "Нотатка : Sonoff S26R2 LAN mode dump"
date: 2021-12-15 01:58:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2021/12/sonoff-s26r2-lan-mode-dump.html
---

RUN eWelink app in LAN mode

swicth on/off device from app

  1.  tcpdump -nvX -i wlan0-1 -n  host  192.168.201.12 and dst port 8081 and not port 5353

  

[![](/assets/images/blog/8321bd847cb4b411-86031bb3552b807a.jpg)](https://blogger.googleusercontent.com/img/a/AVvXsEjdS2gKgv9MXfQHkB-CP3jG2k0R4_UTmj709kKGgxTiu34f1Iw1EIMrzy-gIG_ZhblJB2p4iXd8HVC6rFT3IgU-8lyH3CT0fhhIk2qIGmwhUxqXnMFTC56MKSuGagBEsUk4M8QGkF-MM-lkYK-JhmaZTpe0tac2kMBnEnwMcIrjkYbSx9_xJql6j8rS-g=s1149)

  

2. REST-API

[![](/assets/images/blog/8321bd847cb4b411-2db75d2d918f749e.jpg)](https://blogger.googleusercontent.com/img/a/AVvXsEh9qHGyJ3fQmqr08hL0QJ3qaYgxd_yJYRP9ctcaMAt7lOz0xn9Mvz57KXl511B49ofgQpyKuFtEN2HVQKBaD7ARm58HmMzGy7eEJT_onJcbQYGiKVVp__is0MJSbZwZUTl3Ns3w3MXzDSSptN0Uk9iDD9FrFZErnZ6ajZiuOeklR_0jYdKQuRtMRBZpuA=s862)
