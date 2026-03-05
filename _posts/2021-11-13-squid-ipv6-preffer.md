---
layout: post
title: "Squid ipv6 preffer"
date: 2021-11-13 01:38:00 +0000
tags: ["adimistration", "internet", "ipv4", "ipv6", "proxy", "squid"]
blogger_orig_link: https://lexxai.blogspot.com/2021/11/squid-ipv6-preffer.html
---

<https://wiki.squid-cache.org/Features/IPv6>

```
acl to_ipv6 dst ipv6

# Magic entry. Place first in your config. This makes sure Squid has the IP available.
http_access deny to_ipv6 !all

tcp_outgoing_address 10.255.0.1 !to_ipv6
tcp_outgoing_address dead:beef::1 to_ipv6
```
