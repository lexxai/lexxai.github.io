---
layout: post
title: "MTA Postfix with HAProxy + pfSense GUI"
date: 2020-11-11 00:55:00 +0000
tags: ["administration", "email", "haproxy", "pfSense", "posfix", "security", "server"]
blogger_orig_link: https://lexxai.blogspot.com/2020/11/mta-postfix-with-haproxy-pfsense-gui.html
---

Якщо треба збалансувати навантаження на поштові сервери, можна створити це за допомогою HAProxy.

[https://www.haproxy.com/.../efficient-smtp-relay.../](https://www.haproxy.com/blog/efficient-smtp-relay-infrastructure-with-postfix-and-load-balancers/?fbclid=IwAR0-iE9omD0NajWnHK1ogWCEczxBw-h-6iHdbpZ7zSqhEUHCRu73pwvvi_U)

Якщо треба додати опцію "send-proxy" у pfSense GUI для backend можна зробити це в розділі "Per server pass thru"

[![](/assets/images/blog/6b069bc36abcddb2-7c9d56739e5eb613.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh5N5uvPxdQrLVBgvtwz2aQJC8uEgeq8ZZ0JZvhI1K7klgTNitK6tUZjPgmf1_d7csyqGERpoAasb4_t5xkDOMyM4C9LGPLsqe59P-lZREWUslV3hk1Ypgy_b-W-rrAKE-zvVK6kvhEstmL/s1181/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-11-11+02-39-12.png)  
*send-proxy opton for backend*
