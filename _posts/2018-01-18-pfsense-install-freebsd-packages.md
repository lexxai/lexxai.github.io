---
layout: post
title: "pfSense install FreeBSD packages"
date: 2018-01-18 20:06:00 +0000
tags: ["FreeBSD", "package", "pfSense"]
blogger_orig_link: https://lexxai.blogspot.com/2018/01/pfsense-install-freebsd-packages.html
---

##### [Re: 2.3 - How to install other FreeBSD packages / repositories?](https://forum.pfsense.org/index.php?topic=109827.msg644067#msg644067)

« **Reply #7 on:** August 01, 2016, 07:20:04 am »

This does work in pfSense 2.3.2:  
1. Make sure "enabled" key is set to "yes" in /etc/pkg/FreeBSD.conf  
2. Change "enabled: no" to "enabled: yes" in /usr/local/etc/pkg/repos/FreeBSD.conf  
3. Change "enabled: no" to "enabled: yes" in /usr/local/etc/pkg/repos/pfSense.conf for FreeBSD repo  
4. Now you're able to install packages from FreeBSD official repo

  
https://forum.pfsense.org/index.php?topic=109827.msg644067#msg644067
