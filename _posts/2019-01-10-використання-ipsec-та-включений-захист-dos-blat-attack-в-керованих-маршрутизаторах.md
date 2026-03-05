---
layout: post
title: "Використання IPSEC та включений захист DoS (Blat Attack) в керованих маршрутизаторах."
date: 2019-01-10 00:35:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2019/01/ipsec-dos-blat-attack.html
---

Так увімкнув функціонал захист [DoS](https://uk.wikipedia.org/wiki/DoS-%D0%B0%D1%82%D0%B0%D0%BA%D0%B0) (DoS-атака) на керованому маршрутизаторі необхідно уявляти як і на що це впиває.  
Я випадково заблокував роботу [VPN](https://uk.wikipedia.org/wiki/VPN) [IPSEC](https://uk.wikipedia.org/wiki/IPsec) між двома філіями, увімкнув усі типу захисту DoS у керованому [маршрутизаторі L2](https://uk.wikipedia.org/wiki/%D0%9C%D0%B5%D1%80%D0%B5%D0%B6%D0%B5%D0%B2%D0%B8%D0%B9_%D0%BA%D0%BE%D0%BC%D1%83%D1%82%D0%B0%D1%82%D0%BE%D1%80).  
Проаналізував з часом що було зроблено з'ясував що блокування  [IKE](https://uk.wikipedia.org/wiki/Internet_Key_Exchange) Phase 2 було через захист DoS Blat Attack.  
*Blat Attack - різновид DоS атаки в якому порт джерела дорівнює порту призначення.*  
А так як початкові фази IKE використовують для з'єднання симетричний порт UDP 500 як у відправника так і у отримувача, то це як раз ця ситуація.  
Тому треба захист DoS Blat Attack виключити щоб запрацював [VPN](https://uk.wikipedia.org/wiki/VPN) [IPSEC](https://uk.wikipedia.org/wiki/IPsec).  

[![](/assets/images/blog/241483ff9dec214d-b63f2e51220137df.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiXFm6tDG5NPdZinSqUrGycxazBKO6MwNA1p9JTfZSu0_4rokElDvt1QgVc_4Yq4SSTJZlJMbYqvJHn_kZUl2MLgWyb6Or2salqsoTYvgPVHMKdh9W0OPNwyIdz6cZ3Dx6AiLMZ_4EH3JiD/s1600/ipsec-dos-blat-attack.PNG)  
*DoS Defend, L2 Management switch, TP-LinkJetStream 24-Port Gigabit L2 Managed SwitchT2600G-28TS*

У використаній конфігурації порт [ISP](https://uk.wikipedia.org/wiki/%D0%9F%D0%BE%D1%81%D1%82%D0%B0%D1%87%D0%B0%D0%BB%D1%8C%D0%BD%D0%B8%D0%BA_%D0%BF%D0%BE%D1%81%D0%BB%D1%83%D0%B3_%D0%86%D0%BD%D1%82%D0%B5%D1%80%D0%BD%D0%B5%D1%82%D1%83) , і  порт [WAN](https://uk.wikipedia.org/wiki/WAN) програмного шлюзу [pfSense](https://www.pfsense.org/) підключений до маршрутизатора L2, з організацією окремого [VLAN](https://uk.wikipedia.org/wiki/VLAN).
