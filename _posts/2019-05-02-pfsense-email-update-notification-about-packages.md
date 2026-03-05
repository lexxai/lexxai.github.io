---
layout: post
title: "pfSense email update notification about packages"
date: 2019-05-02 21:20:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2019/05/pfsense-email-update-notification-about.html
---

Є задача отримувати на пошту результати періодичної перевірки застарілих застосунків що встановлені додатково до pfSense на кшталт squid, pfBlockerNG ...  

### Рішення:

1. Створюємо Email Report, додаємо опис, налаштовуємо періодичність, зберігаємо.  
2. Повертаємося до редагування щойно створеного  Email Report, і додаємо команду: '/usr/local/sbin/pfSense-upgrade -c', зберігаємо.  
  
  

[![](/assets/images/blog/3e0453db9f194916-9078cdebdb49b712.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjNBBy6hese-6kShOEmucnjp6qc6xnAZd_nbH1u-aBer6bizihrFg9hOVlS78ZgG9onY-MZSRm1YTJzjmT6WrS5n5Z2ejoy_xInNwmzEvAy7iudUFtYaaM8sGpevpEwJ4IQaSoAH0NipVvi/s1600/pfSence-upgrade.PNG)  
*Email Report pfSense-upgrade -c*

  
  
  
За матеріалами:  

* <https://www.facebook.com/groups/pfsense.official/permalink/2005509943090559>
* <https://forum.netgate.com/topic/137707/auto-update-check-checks-for-updates-to-base-system-packages-and-sends-email-alerts/2>
