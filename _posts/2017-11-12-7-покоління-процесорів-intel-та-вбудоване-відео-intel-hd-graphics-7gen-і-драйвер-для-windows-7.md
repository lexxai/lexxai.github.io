---
layout: post
title: "7 покоління процесорів Intel та вбудоване відео Intel HD Graphics 7gen і драйвер для Windows 7"
date: 2017-11-12 14:16:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2017/11/7-intel-intel-hd-graphics-7gen-windows-7.html
---

Оновлюючи один с старих офісних комп'ютерів замовив конфігурацію на основі процесора 7 покоління від Intel Core i3-7100 3.9GHz/8GT/s/3MB (BX80677I37100) s1151 BOX.

Але після перевстановлення корпоративної ліцензійної Windows 7 з'ясував що драйвер відео карти (Intel HD Graphics 630) не встановився, а залишився стандартним.

[![](/assets/images/blog/8bc2631b4699ad97-470d451167d4b7e2.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgv4-PkT6ViqlM_rNB_zGcFIEU9UfDLyTgruTYMhiMrZPBwiujiw1ik9FDH6t6OU8-YLmqz0T8zC0snO_k7VC9PmJUo-pcjDtHLJFhv3fM4nS1anjEBkKyd0bF4C8gbYR10T2bzv6OZmTAd/s1600/stdriveri3.PNG)  
*стандартний VGA*

На сторінці [завантажень](https://downloadcenter.intel.com/product/97503/Graphics-for-7th-Generation-Intel-Processors) можна завантажити драйвера, і в нотатках до них почитати що для 7 покоління драйвера зараз тільки для Windows 10.

Оновити до Windows 10 корпоративну не вдавалося так як не було відповідної [підписки](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx).

Тому у пошуках рішень знайшов таке цікаве рішення як редагування ini файлу.

Для себе знайшов рішення, як використання  попередньої версії драйвера, з комплекту до материнської плати ASUS, і вибирання опції оновити драйвер, а не встановлення через setup.exe.    
Також операційна система при оновленнях дає таке повідомлення:  

[![](/assets/images/blog/b9051ba493846b94-e5d450663e6d355f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiJnveNwck_Jpp41EouzAbUapP8mHui6dvK1u8bZb_ROQ_RXcMgmlPljcA_7IslHVwstmWDvmCAbNVXB0AAboVS6D8LrHPdOk37xHkoKzlFEXshP4AhFTxZj6neSjCVQV9oxOgGTmfWHAFX/s1600/7gencpu.PNG)  
*Несумісне устаткування Windows 7 and Intel CPU 7 gen*

P.S. Цікаве доповнення, сьогодні (2017-11-16) успішно оновив, майстром оновлення, активовану Windows 7 Pro (GG) Volume License до версії Windows 10 Pro (1709), активація зберігалася.
