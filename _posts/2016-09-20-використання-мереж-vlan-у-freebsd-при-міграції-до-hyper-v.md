---
layout: post
title: "Використання мереж VLAN у FreeBSD при міграції до Hyper-V"
date: 2016-09-20 18:20:00 +0000
tags: ["adimistration", "FreeBSD", "Hyper-V", "Virtualization", "VLAN", "адміністрування", "мережева"]
blogger_orig_link: https://lexxai.blogspot.com/2016/09/vlan-freebsd-hyper-v.html
---

Портував [FreeBSD](https://uk.wikipedia.org/wiki/FreeBSD) сервер до віртуального середовища під керуванням [Microsoft Hyper-V.](https://en.wikipedia.org/wiki/Hyper-V) І за звичай хотів використовувати налаштування які я робив у FreeBSD для віртуальних мереж, де є одна мережева карта "fxp0" і з неї "витягуються" усі віртуальні мережі vlan101...vlan1000:  

```
rc.conf:

cloned_interfaces="vlan101 vlan102 vlan103 vlan104 vlan1000"
ifconfig_vlan101="inet 192.168.101.1 netmask 255.255.255.0 vlan 101 vlandev fxp0"
ifconfig_vlan102="inet 192.168.102.1 netmask 255.255.255.0 vlan 102 vlandev fxp0"
ifconfig_vlan103="inet 192.168.103.1 netmask 255.255.255.0 vlan 103 vlandev fxp0"
ifconfig_vlan104="inet 192.168.104.1 netmask 255.255.255.0 vlan 104 vlandev fxp0"
...
ifconfig_fxp0="up"
```

Але з'ясувалося через 'tcpdump -i fxp0 -eee', як у жахливому сні - що десь чути чую, а сказати нічого не можу .  
Після аналізу з'ясував що так і потрібно, щоб віртуальні середовища були ізольовані один від одного.  
Вихід з цього простий треба у налаштування віртуального середовища створити стільки мережевих адаптерів скільки віртуальних мереж Вам потрібно. Для тегованих так і не тегованих портів створюються окремі адаптери:  

[![](/assets/images/blog/154971deaf655f39-d95660e93af4d4b6.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjcoq35-0PWrAuPAILNVFDy0_e-wQrDjCQ5dOioDzJYozmYN2HHKnBLiCdALaUuzptO-zG2GRVQk0rMWcjFPbW9swt0XWfNvM2gqxXCeY7P4zwpUbFUwtY9_NYNeV22hDdT3pC_gG9quV53/s1600/hyper-v-freebsd-vlan.PNG)  
*Визначення TAG ID port VLAN для мережевого адаптера у Hyper-V*

[![](/assets/images/blog/b4fd795296f997c3-9b12452d8282dc8a.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjbzwiy7HXigyeGWUWQa2kf9TT15zV6bG1u2C6_e2nKR0UDUQMtMcHyekQS11X1DEq9af-1K5psds78qMSm6AHIphK_VZgunB-sYjISmCviEz5dLgF6RVPoHlp_L8LIwJ-uAm-2qkKN9KIg/s1600/hyper-v-freebsd-vlan-untag.PNG)  
*Визначення UNTAG port для мережевого адаптера у Hyper-V*

[![](/assets/images/blog/d6330d72aab96975-a097d56dc7c9201f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiPTWJn3k2uYZXXbrQDNMTRKKX5rFLF1uXg8N9kzHT4r6YF5pS1X9rrb0nXGfpGUApv9kSgqTqH4LzR-5psS_6d0zUr73D-GBW_qZ69-RQmsLdTbQC6B4Ch5pVunWR2Eat5x54YFvBPoKQa/s1600/hyper-v-freebsd-vlan-ifconfig.PNG)  
*Результат віртуальні мережеві адаптери (hn) Hyper-V у FreeBSD 10*

Хочу зазначити що перший мережевий адаптер у визначений у Hyper-V, є hn0 адаптером у FreeBSD 10.  
Для зручності можна перевизначити імена мережевих адаптерів для зручності:  

```
ifconfig_hn0_name="lan0"
ifconfig_hn1_name="vlan101"
ifconfig_hn2_name="vlan102" 
ifconfig_lan0="inet 10.0.0.1 netmask 255.255.255.0" 
ifconfig_vlan101="inet 192.168.101.1 netmask 255.255.255.0"
ifconfig_vlan102="inet 192.168.102.1 netmask 255.255.255.0" 
```
