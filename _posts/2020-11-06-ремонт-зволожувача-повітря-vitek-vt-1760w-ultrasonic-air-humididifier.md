---
layout: post
title: "Ремонт зволожувача повітря \"VITEK VT-1760W\" (Ultrasonic air humididifier)"
date: 2020-11-06 20:56:00 +0000
tags: ["Aliexpress", "hardware", "repair", "кешбек", "ремонт"]
blogger_orig_link: https://lexxai.blogspot.com/2020/11/vitek-vt-1760w-ultrasonic-air.html
---

Маю у домашньою побуті доволі старий зволожувач повітря VITEK VT-1760W, але він з часом перестав працювати.

[![](/assets/images/blog/a76850821ab840eb-c6037054cf8d6e68.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjFjrpzIltb8wCflR6clhvAFFIXvoouArWVnrrKTGlc-cEfThpRar1XyCHURhcg4NAN9BgwdfZmum1f2vixguhcgbOnzVS_zFo-wTgORFM4v2ueF7fa3cDDwEIXEcfUnOmF2fCw466Vlp5q/s1968/IMG_20201106_210741.jpg)  
*Модель зволожувача повітря VITEK VT-1760W*

Були спроби самостійно відремонтувати. Замінював елемент генератора
пару діаметром 25мм, замінював транзистор BU406, але в цілому не було
достатньо кваліфікації для з'ясування проблемного місця.

Тому було
рішення купити новий зволожувач повітря, але роблячи вибір, я зрозумів
що всі вони майже однакові за схемою, і мій зволожувач ще дуже і нічого.
До того ж пошук запчастин показав що в [Аliexpress](https://s.click.aliexpress.com/e/_A8hFZr ) можна знайти вже готові плати і навіть у комплекті з блоком живлення, ось я і знайшов [плату для елемента 25мм на 38V потужністю 33W](https://s.click.aliexpress.com/e/_A8hFZr )., вартість $10.9.

Тому вирішив [придбати ремкомплект](https://s.click.aliexpress.com/e/_A8hFZr ) і таким чином відремонтувати свій старий зволожувач.

[![](/assets/images/blog/1157f560f9ab8517-64e135ffa35d6713.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhoOvroCkdl7iv7gg1x1F0blNNIOwyQJDLv-q7QfhBVlFm4m8eHVpbHi6Z4ws3WBl2PdQwM-13eA6zsjD-zyahxOakWfEWcHe5LWMbRcr0BZ7PLnJqUwfORQ-4shQiJZH-A9eQOzl71BYtM/s3676/IMG_20201106_193119.jpg)  
*Набірдля ремонту зволожувачаотриманий поштою*

Єдина проблема те що у продавця не було схеми підключення, але є ресурси де я знайшов схему підключення.

[![](/assets/images/blog/705d1a39b9524552-d63d134e04e1e05c.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjXZyG67oJK9gOLMsi5ysQ8FKm_DlN26MscietaRHgGQptCEt6EaKvCLLC1GcNGX6qvvLeizM40o2YqAP4wzpr7KAQTFDAaIXU4LHPpohtqcUjlFYiY0La1-suq9SDwFzG1g9S9EtsKlpZ9/s662/Screenshot_20201106_200817%257E2.jpg)  
*Схема підключенняплати зволожувача ZO-854 (Z0-854)*

Виникли певні особливості підключення до VT-1760W [плати зволожувача ZO-854 (Z0-854)](https://s.click.aliexpress.com/e/_A8hFZr ) у області регулятора потужності, так як у плати [ZO-854 (Z0-854)](https://s.click.aliexpress.com/e/_A8hFZr ) використано змінний резистор (4.8К) з роз'ємом на три контакти, а у VT-1760W на два контакти. Але якщо підключити як на зображенні, то все працює. З передньої панелі йде два проводи з двома контактами, один підсвічування інший регулятор електричного опору (від 0 до 5К). Знайти хто є хто можна прибором у режими заміру електричного опору.  

[![](/assets/images/blog/9c9535cefa1393bc-34361c0f0cf58984.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhYM3zi4Ar9uSpZNPro5zK10ja-3Mai0IzBBW_OnrH5cAm9l9FbPeVUXU0KfVVhq9jadMGUEl9gyKuEnho30IZuGD7g0hMjYJNmNklonnA7OBrfdmxppoQ6uEMZHL4-OQDaf6lae4JJjLgw/s1408/IMG_20201106_204743.jpg)  
*Підключенняплати зволожувача ZO-854(Z0-854) у VITEK VT-1760W*

Після монтажу, залишилося невикористаний вимикач з що був у комплекті , залишив у копусі постійно ввімкнутий так як використовується вимикач з схеми VITEK VT-1760W.

[![](/assets/images/blog/1a7453a874243981-34361c0f0cf58984.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiSIiFDEe6m7yzHqEXu9uKb8SbJBSzjK0mifNoQusZlH8dAFUWHAOaw5RAuWwCF1gonjI3qjzNqt4O4kwN5gZJLvHeaITk-Yl-vRPCtbyBdZ0zvyg10S2n7mi4xaOOmYxLvgZs64j14JBoj/s4159/IMG_20201106_204743.jpg)  
*Зволожувач повітря VITEK VT-1760W після заміни*

В роботі все працює гаразд, потужність регулюється, змінився колір світлодіоду, коли бак з водою горить червоний, а раніше було навпаки.

[![](/assets/images/blog/5f37c2530510ceb2-ef5bc87a36bf4a6c.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjLq4WPiEE-0BwoaB3wgUgKeImoUApnYNk4yGZw1pdTqzdRpXnG1Pwega7Zok4nKLscirhpJNEAQLDwz7rbRgnLllgsWe5wbuZR2_OhP-jhKOrBfuJMrjs7dt-uCCuuGysOcPk4HVSU2INq/s4057/IMG_20201106_205807.jpg)  
*Зволожувач повітря VITEK VT-1760W в роботі*

 До речі купити на Алі можна з певною знижкою якщо придбати через систему [payback.ua.](https://payback.ua/41Shk)  

[![](/assets/images/blog/5821a3dfe82bbea5-0ed1d7e61dee476a.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEidhXu73EzHhuFbpHUqrUCjPNM8Nhs43TmgL_XmY1GI1bMVL3CCp8_xIaM3I2YKA7GuCXR7oQz58Jto6EPvMjw20lX9YpCRHtEsNbxBqhwwedqHTjyw0Z3sOW22we2oyYRxdSBKUOcsmvaB/s695/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-11-06+22-51-23.png)  
*Покупказволожувача ZO-854та знижка на неї в $0.39*
