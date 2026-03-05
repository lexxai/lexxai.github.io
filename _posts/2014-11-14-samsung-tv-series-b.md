---
layout: post
title: "Samsung TV series B"
date: 2014-11-14 17:43:00 +0000
tags: ["Samsung", "tv", "wifi"]
blogger_orig_link: https://lexxai.blogspot.com/2014/11/samsung-tv-series-b.html
---

Деякі нотатки.  
  
1. Очистити попередні зображення для відеофільмів у медіа програвачу що вбудований до тв можна за допомогою очищення теки mtd\_contents.  
  
2. USB WiFi Dlink DWL-110 FW A1  
(USB: 07D1:3C07 D-Link System Wireless G DWA-110 Adapter)  
За [описом](http://wiki.samygo.tv/index.php5/Using_NoN-Samsung_USB_WiFi_dongles_with_TV) і використання драйвра  [rt73.ko](http://api.viglink.com/api/click?format=go&jsonp=vglnk_141626799683415&key=14bc32aad663f286fb16c0eaf8a61050&libId=ac268757-7765-4587-935a-7ac64542cc32&loc=http%3A%2F%2Fwiki.samygo.tv%2Findex.php5%2FUsing_NoN-Samsung_USB_WiFi_dongles_with_TV&v=1&out=http%3A%2F%2Fdownload.samygo.tv%2FB%2520Series%2FKernel%2520Modules%2FSamyGO%2520WiFi%2520Extension%2520Addon%2520v0.02.zip&ref=http%3A%2F%2Fwiki.samygo.tv%2Findex.php5%2FContent_Library_applications_list&title=Using%20NoN-Samsung%20USB%20WiFi%20dongles%20with%20TV%20-%20SamyGO&txt=here), результат не втішний - WiFi не працює.  
А ось якщо використати спеціальний драйвер rt73.ko :  [rt73-dlink-110](http://download.samygo.tv/Others/rt73-dlink-110.zip) - то WiFi запрацював !!!
