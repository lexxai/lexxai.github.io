---
layout: post
title: "Нотатка для себе:  Обмеження доступу до WiFi корпоративгої мережі зі спілним сервером FreeRadius"
date: 2020-09-11 01:40:00 +0000
tags: ["access", "certificate", "eap", "FreeRadius", "open-wrt", "pfSense", "security", "self signed certificate", "VLAN", "wifi", "Wireless"]
blogger_orig_link: https://lexxai.blogspot.com/2020/09/wifi-freeradius.html
---

Є
[бездротова точка доступу WiFi](https://uk.wikipedia.org/wiki/%D0%91%D0%B5%D0%B7%D0%B4%D1%80%D0%BE%D1%82%D0%BE%D0%B2%D0%B0_%D1%82%D0%BE%D1%87%D0%BA%D0%B0_%D0%B4%D0%BE%D1%81%D1%82%D1%83%D0%BF%D1%83)
що має декілька віртуальних WiFi мереж котрі розділені за допомогою
[VLAN](https://uk.wikipedia.org/wiki/VLAN). Так є
корпоративна мережа з доступом до серверів компанії, є звичайна мережа
корпоративних користувачів для доступу до мережі інтернет тільки, ні і є
гостьова мережа з певними обмеженнями.

Віртуальні WiFi:

* lexxai
* lexxai-co
* lexxai-guest
* lexxai\_5g
* lexxai-co\_5g

Корпоративна WiFi мережа використовує доступ за технологією
[WPA2-EAP](https://uk.wikipedia.org/wiki/Extensible_Authentication_Protocol), з авторизацією в корпоративному
[FreeRadius](https://en.wikipedia.org/wiki/FreeRADIUS)
сервері. Гостьова мережа має доступ з відомим ключем WPA2-PKS, але з Captive
Portal. Додаткові мережі що працюють у діапазоні 5ГГц мають суфікс '\_5g'.

### Налаштування FreeRadius

Усі віртуальні wifi мережі налаштовані з підключенням до спільного Radius
сервера, як різні клієнти.

[![](/assets/images/blog/fa19089f7bb0dc9f-0a41fe8d449cc74a.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgaRx-1rByg0CcjzSTX88sS9pgBqznrKGgza9Zezd3jXtixhTX1jxCzeOEJPtm2vi_TZbR5FqDsyHvK5WS7boPVSocjClPYzyAt6d4Bz4VQoD2Z_ErJJhBUrvpHgYD2oGor4VG6EFt_oF9F/s1197/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-09-11+03-45-53.png)  
*FreeRadius clients*

Кожен користувач має персональний сертифікат котрий згенерований
корпоративним центром сертифікації.

[![](/assets/images/blog/ec9194d8518cd7ab-e18ea2f59d8a3d2d.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgkNRG0j8xv54X_Q678rYL8iZBvAIqt9a_3Ara7xq4oKWv3_qBBw1zRYaE9Lx4Gbpn87yuPlPqycIRcxdVmeMC1PCvTOiLxMGJjSQ_mGoB_-9px92KvX8lgnPHl9hpLivKVA_Ccsa6zECb1/s1178/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-09-11+03-56-09.png)  
*користувачів персональні сертифікати*

#### Налаштування FreeRADIUS: EAP

[![](/assets/images/blog/7459eadf680c4645-976aa9a29971b074.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg8xFZjAA2KAVYsksJp5wgW3PS7lgDl6vUBGryLV_bwSZBHkVhW8EMYxe3ALlUA73ORvH39mLB0T3HHFsBFfMqeKl7eFja89nTwrensU6r4Hji0wYVuqkF3Plqdap1ASFWekU5VpIlKF2UB/s882/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-09-11+04-01-53.png)

[![](/assets/images/blog/516a5407641a41b7-f4f988e0455aa319.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjr0HM4uKuAH86LxMKfop5iIXDZIfloDZ77gs7J_y4TzuZ5MqmOuUPa0dU0rgvpfQN4ctGrghUtCyG9qpl-UQe6rSNe24cXEFda_K_Re3y02yfOnCRiM-bACwE6zwVB7vKwEQ1oIOlLfbw-/s876/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-09-11+04-02-15.png)  
*FreeRADIUS EAP*

### Сертифікати користувачів

Таким чином WiFi авторизацію пройдуть тільки клієнти у котрих налаштовані
сертифікати у мобільних пристроях.

[![](/assets/images/blog/57a3e705d8b5b58e-4fd24acb6dc1e0e1.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiAyoxmJrqDQt0a7urAF72IHMyRARwMtA_CMlz41yW1royWKiaYQd0QiUzguWszY-U4V9x8Fg7RTJpLUq-FcJjaedgbyjAG37r6to-IG_ssgP-AtE5DW75V8IaGZP47zMNTUY9ZN21G0Y_B/s1081/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-09-11+04-06-46.png)  
*Експортування сертифікату з ключем в форматі PKCS#12 .p12*

Але є але, усі сертифікати виданні тим центром сертифікації що вказаний у
налаштуваннях FreeRADIUS будуть підключатися до усіх мереж WiFi що
використовують цей RADIUS сервер, за виключенням тих сертифікатів що були
додані до файлу відкликання (SSL Revocation List).

### Розподілення доступу

Для розподілення користувачів, треба створити файл поліції у теці в FreeRadius
- policy.d.

touch /usr/local/etc/raddb/policy.d/lexxai-corporate

vi /usr/local/etc/raddb/policy.d/lexxai-corporate

lexxai-corporate:

```
filter_lexxai_corporate {  
        if ( &request:Called-Station-Id =~ /^[0-9a-f]{2}-[0-9a-f]{2}-[0-9a-f]{2}-[0-9a-f]{2}-[0-9a-f]{2}-[0-9a-f]{2}  
:lexxai-co(_5g)?$/i ){  
         if ( &TLS-Client-Cert-Common-Name == &User-Name  &&  &TLS-Client-Cert-Common-Name =~ /^wifi-lexxai-co[_-]{1}.*  
$/i ) {  
            noop  
         }else{  
            reject  
         }  
        }  
}
```

Додати створену поліцію (filter\_lexxai\_corporate) до налатування розділу
post-auth в файлі /usr/local/etc/raddb/sites-enabled/default

[![](/assets/images/blog/007b4b0433bf80a8-85555405df169095.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg62OgvQ719khj0ylJ0tQp0HgZFv2g6P6UadKqZw7KWl53AZy4JBWCm_GFh4Ly8ihlIJzF8qhE0aSY5EoUP-79zJp7vbZv2ATdsEaQOVH9AF0EOUQaljcUNVEAstX7yWg_1Gc0gkLnGlD3-/s639/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-09-11+04-25-12.png)  
*Додавання в post-auth фільтра filter\_lexxai\_corporate*

Таким чином, після авторизації користувача буде зроблена додаткова перевірка
частини імені користувача 'lexxai-co' з назвою віртуальної мережі Wifi.

Назва передається у атрибуті  Called-Station-Id  у вигляді
XX-XX-XX-XX-XX-XX:lexxai-co, де XX...XX це мак адрес.

Ім'я користувача має спів падати з тим що записано у сертифікаті:  
&TLS-Client-Cert-Common-Name
== &User-Name

І це ім'я має починатися з "wifi-lexxai-co".

Тільки виконання всіх цих умов надає доступ до корпоративної мережі
"lexxai-co".

P.S. Якщо змінити налаштування  FreeRadius у графічному інтерфейсі
pfSense то файл /usr/local/etc/raddb/sites-enabled/default  буде
перезаписаний, і треба знову додати фільтр у розділ post-auth.

Для  автоматизацї додав скрипт:

```
#!/bin/sh

WDIR=`dirname ${0}`
RADD=/usr/local/etc/raddb
if [ ! -f "${RADD}/policy.d/lexxai-corporate" ];then
 echo "policy not exist"
 cp "${WDIR}/ese-corporate" "${RADD}/policy.d/ese-corporate"
fi

if [ -f "${RADD}/policy.d/ese-corporate" ];then
 POLAPPLY=`grep filter_lexxai_corporate  "${RADD}/sites-enabled/default"`
 if [ -z "${POLAPPLY}" ];then
  echo "RADIUS policy was not applied"
  patch -f -s  "${RADD}/sites-enabled/default" <  "${WDIR}/def.diff"
  if [ -f  "${RADD}/sites-enabled/default.orig" ];then
   rm "${RADD}/sites-enabled/default.orig"
  fi
  /usr/local/etc/rc.d/radiusd.sh restart > /dev/null
 fi
else
 echo "policy STILL not exist !!!"
fi
```

Де def.diff результат роботи команти diff -u sites-enabled/default sites-enabled/default.orig.
У мене це так, у Вас буде інше:

```
--- default.orig        2020-09-21 10:57:54.480212000 +0300
+++ default     2020-09-21 11:03:11.773411000 +0300
@@ -148,6 +148,7 @@
 #                      State := "0x%{randstr:16h}"
 #              }
 #      }
+       filter_lexxai_corporate
        update {
                &reply: += &session-state:
        }
```

Додав до розкладу \*/5 через crone.
