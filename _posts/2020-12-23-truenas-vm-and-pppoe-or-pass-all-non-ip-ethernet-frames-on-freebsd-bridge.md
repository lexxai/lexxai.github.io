---
layout: post
title: "TrueNAS VM and PPPoE or pass all non-IP Ethernet frames on FreeBSD bridge"
date: 2020-12-23 19:45:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2020/12/truenas-vm-and-pppoe-or-pass-all-non-ip.html
---

Базово, якщо створити віртуальну машину у [TrueNAS/FreeNAS](https://www.truenas.com/) то дещо обмежено у користуванні: мережа підключається через bridge а він не пропускає не IP пакети.

Це тому-що  [bridge](https://www.freebsd.org/cgi/man.cgi?bridge(4)) в <TrueNAS/FreeNAS> має  ввімкнуте налаштування:  [net.link.bridge.pfill\_onlyip](https://www.freebsd.org/cgi/man.cgi?bridge(4))=1, що обмежує проходження пакетів тільки [протоколу IP](https://uk.wikipedia.org/wiki/Internet_Protocol#%D0%A1%D1%82%D1%80%D1%83%D0%BA%D1%82%D1%83%D1%80%D0%B0_IP-%D0%BF%D0%B0%D0%BA%D0%B5%D1%82%D1%83), (set to 0 to unconditionally pass all non-IP Ethernet frames).

Тому встановимо net.link.bridge.pfill\_onlyip=0 в системних налаштуваннях [TrueNAS/FreeNAS](https://www.truenas.com/):

[![](/assets/images/blog/eac5c780f98b6297-b70c33d695e611f8.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg9nJj-iAWF4zMipksfGByRFcBIjAF7GKH2xAiHlj3m5Tm_Zb2DXGvcHeqw600zxrWfcNcHFFvuGgWLZSc9HJlnFvQZEhToTb0BLgP-FFrE5bxoEb2o87GaaRWDD7Qzipy6gSzlKfIb8OzN/s466/tnas-pppoe-01.PNG)  
*Sysctl: net.link.bridge.pfill\_onlyip*

Після перезавантаження віртуальна машина зможе отримати всі non-IP Ethernet пакети котрі недохідні для [PPPoE](https://uk.wikipedia.org/wiki/PPPoE), [DHCP](https://uk.wikipedia.org/wiki/DHCP) або іншого  сервера наприклад в [pfSense](https://www.pfsense.org/).

За матеріалами:

* [PPPoE in a VM | TrueNAS Community](https://www.truenas.com/community/threads/pppoe-in-a-vm.88509/#post-613377)
* [Running pfSense as a VM guest on FreeNAS 9.10 host using Bhyve – David Nelson](https://davidnelson.me/?p=439)
