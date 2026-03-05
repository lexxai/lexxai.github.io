---
layout: post
title: "USB over Enternet"
date: 2014-04-18 12:09:00 +0000
tags: ["ethernet", "usb", "usb ethernet", "VM"]
blogger_orig_link: https://lexxai.blogspot.com/2014/04/usb-over-enternet.html
---

[![](/assets/images/blog/2faeef320f9fad0a-072821d39f47cce8.gif)](http://www.incentivespro.com/images/box_rdr.gif)

  
А таки є проблема передачі USB пристоїв до віртуальних серверів, таких як ключі захисту тощо.  
Рішення:  
 -  встановити десь у мережі сервер ліцензування що має фізичний доступ до ключів ліцензування, і підключатися до нього;  
- встановити апаратний пристрій для спільного використання USB пристоїв через мережу;  
- встановити програмні засоби для спільного використання USB пристоїв через мережу;  
  
  
Ось одне з програмних рішень перенаправлення USB пристроїв через мережу:  
[Share USB devices between Linux and Windows platforms!](http://www.incentivespro.com/crossplatform-solution.html)  
  
Апаратні рішення: [USB device access with #HyperV child VMs? YES! #ITCamp](http://blogs.technet.com/b/keithmayer/archive/2012/05/16/usb-device-access-with-hyperv-child-vms-yes-itcamp.aspx)  
  
При викорстанні VMware vSphere (ESXi) можливо зробити так:  
How to configure ESXi 5.0 for USB 3.0 passthru to a Windows Virtual Machine:
