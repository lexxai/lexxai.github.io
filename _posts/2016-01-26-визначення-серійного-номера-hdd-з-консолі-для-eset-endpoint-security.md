---
layout: post
title: "Визначення серійного номера HDD з консолі для ESET Endpoint Security"
date: 2016-01-26 20:25:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2016/01/detect-serial-number-hdd-cli.html
---

[![](/assets/images/blog/fa041ea5517d87b9-f553113c3aba3114.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh6MZ0WJsyIR5MDcpRIS72ONLbc0KRKlRSJQCWWC_t_1WZdX_3mDNtxmybCBG-4pPmuZ_IETDP_HyMNqejaL_nype1MVixmgauQnR6pYxDiVNmO6uzGX3HvOiEpgMPbMx8CpO6Ymlsr76qK/s1600/esa_buss.png)

Адмініструючи локальну мережу де розвернута система з корпоративним антивірусом "[ESET Endpoint Security](http://eset.ua/ua/for_business)" стало завдання на визначення серійних номерів накопичувачів HDD.  
Навіщо ?  
Тому що вбудована система захисту  "КОНТРОЛЬ ПРИСТРОЇВ" має невелику ваду, у деяких випадках вона не може відрізнити змінний накопичувач що підключено через порт USB, від накопичувача що підключено через SATA.  
У такому випадку якщо я створюю правило що забороняє усі змінні накопичувачі, то у таке правил потрапляють і локальні диски котрі підключені через порт SATA.  
Підтримка антивірусу, теж протестувала таку ситуацію і порекомендувала зробити виключення для локальних дисків усіх комп'ютерів локальної мережі за серійним номером.  
У програмі є інструмент що дозволяє заповнити це з локального комп'ютера.  

[![](/assets/images/blog/e8f2df70ff205b39-76a9b14b38d364ae.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhv_mnRme0nZxcLR0ocau1aD0CbknWp0t4O83R2P9mtFk4JWFCyRMEOrm1dgrHHjpFJpoV9XF0m9E9l8l7BaN_fYnzljsrYbZMUEVqiipDmYg40TMjutwcDdyzn4i1Njb6McYoUIAHwzy4U/s1600/eset-dev01.PNG)  
*Заповнити з локального комп'ютера інформацію про пористої.*

Але це корпоративний продукт,  що адміністратор повинен бігати на усіх комп'ютерах організації ?  
Тому я і почав шукати рішення, тому що підтримка не дала відповіді як це зробити засобами "корпоративного" продукту.  Для цього потрібно отримати список усіх серійних номерів накопичувачів комп'ютерів за допомогою запуску команди на комп'ютерах:  

```
wmic  DISKDRIVE get Model, SerialNumber, MediaType, SystemName | find "Fixed hard disk media"
```

[![](/assets/images/blog/be9698e6bb3c56cc-080400ee3e5ba344.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiFBYZyCYJoRzCwIrwtMUjbOLZ3P-1X1g_9r4Uu5rJCFiyhUDkyA7AqKQX5kFyiS8rYer4zKKL0Ef0hXD-2BOqxDvQv_P3tM9BXH9ZDkPHiS2nqaCzUiDiY1rZNW_HtgmmZDPrz-9qqJiNR/s1600/wmic-hdd-sn.PNG)  
*Отримання серійних номерів накопичувачів*

Надалі за допомогою "[ESET Remote Administrator](http://eset.ua/ua/products/for_business/remote__administrator/remote_administrator)", створюємо завдання для централізованого збору усіх номерів, наприклад до одного спільного файлу, або на електрону пошту.  
Отриманий файл, обробляємо і додаю серійні номери до групи пристроїв.  

[![](/assets/images/blog/4ea850a73d99df37-5ba8861f18196a54.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg4Iz8ZMkdE34gjggFsHz0yp9dIBUi2yL5W2xCkQ_uC9VsCszuCGHkVAB6l6dfNUtqjzs6dIuquvE_yF-H8hri5-YLY2Mcecf6LnvZVOBLKOoZxuE5fyy9yYjPVPfu422OjSuk2V4dQNdsn/s1600/eset-dev03.PNG)  
*Додавання серійних номерів до групи пристроїв*

А за групою створюємо правило що дозволяє працювати офісним накопичувачам:  

[![](/assets/images/blog/61b44d33d8969613-dc725a81428e4828.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhUwdjaPrKbfr6f_fL2Qyl-GvOIjnOWEdr55wHkq6uUUbNwr6wXIqtyC1BDrwlS-OUbbSAlHBsobiPXQn39CXMs32UdaydNgSmwFEWmWowmTui_qbLRbEAK5ipG4ktA05kt_v69E89KB6Ej/s1600/eset-dev02.PNG)  
*Правило для дозволу офісних накопичувачів.*

  
  
Але як додавати увесь список пристроїв до групи ?  
У локальній програмі "[ESET Endpoint Security](http://eset.ua/ua/for_business)" є така можливість:  

[![](/assets/images/blog/0a44fe5bd6db7ea6-f0659818cc8ad8a5.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj0OvBjGxZB6cDX83dipIHNaIRbx6RvEqxxLYI15m9KdUtl8Lb74dm3UyqFkYqPCTTMANxL7zozOKpCaYEtMX-wEHMvTN7mkJg2T8Jjjtg3wrwyxPm21urHazep3aKddE3aZwxUuACiCdEe/s1600/eset-import.PNG)  
*Import серійних номерів*

А ось як це зробити у "[ESET Remote Administrator](http://eset.ua/ua/products/for_business/remote__administrator/remote_administrator)"? Допомогу отримав від служби підтримки:  
"Централізованого збору контролю пристроїв в ERA 6 немає. ERA 6 працює з клiєнтами через агента, а також використовуючи політику. Якщо у Вас уже е ПК з налаштованими правилами, можливо зробити наступним чином. Починаючи з версiї ERA 6.2 розробники додали можливість перетворювати файл конфігурації на політику. Можна запросити файл конфігурації з клієнта i натиснути кнопку "Перетворити в політику". Після чого політику можна буде відредагувати, додати інші записи (якщо потрібно)."  
  
Тому отримаю файл зі список номерів для прикладу з одного комп'ютера.  

```
wmic DISKDRIVE get  SerialNumber |find /V "SerialNumber" |find /V "Volume" > snlist.txt
```

У файлі потрібно прибрати пусті рядки. Імпортую цей фал до групи пристроїв на локальному комп'ютері з встановленим "[ESET Endpoint Security](http://eset.ua/ua/for_business)".  

[![](/assets/images/blog/1730a81704c9ab75-3b5903854045c782.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg1P_5oM62yDnBLf1S944TmTaJe11ZsaQsObbh3eEoZIRbh2vpLjDZmVYzByQhuVbcOZxZvC72AfuIfpG-Ko6hn_nf6_3nF9lUIdv962DbhLizBAy6gU0yloDfB3UNwCfq2Z0fPW0Yo_WOm/s1600/eset-import-file.PNG)  
*Імпорт файлу з серійним номерами*

[![](/assets/images/blog/f8cb9018130c874e-50d67b9447e32832.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjCUWiKL9-UPC3YIe2AzwR8QjMrAbrvaqFmMFlmU9CL1CxtMky3vqmgCUW5kven60HUQJkjVr_lxnmbasRB9UK_JWODByIm61gB2awEdpQ6Avzc9MTS45sUClnd4kXNBdZCW5vASdP6hlJI/s1600/eset-imported.PNG)  
*Результат імпорту серійних номерів до групи пристроїв*

Надалі у "[ESET Remote Administrator](http://eset.ua/ua/products/for_business/remote__administrator/remote_administrator)" робимо запит конфігурації з того комп'ютера де ми імпортували файл з серійними номерами.  

[![](/assets/images/blog/afa8470c69d7ba57-588201801a50ccfd.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgnCY7wiD8sxmfZw4BsZqQc-YOjC5SyHcZHhD-NX2l5Kd0uVShSEfu_-VGZrNfNM1VRIHHiY_eQwIg1IbNupHLZ0Y-IzG5hK0O-FU_ltgOwL2rHjYVh4y5zQv_Xzikz-7barvWYLy3eOVQR/s1600/era-request-configuration.PNG)  
*Запит конфігурації комп'ютера у ERA*

 Після запиту зачищаємо отриману конфігурацію, від не потрібних налаштувань, і залишаємо тільки те що пов'язано з контролем пристроїв.  
  

[![](/assets/images/blog/381e89207950bc13-819e01d854570ca8.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg41h_Arn10rdrdEdZVEpf-0kcCFHq96Qm2g7J-I-LD97o58CDn4nD3V1ByBdUJ2AIN5kwAIEZDBA-DTZlmYLRnC7QPgPKSh19r_R_tI-gVLLVePJtOUUTyLmIgi4PJwEFEzJr8OdORm1BD/s1600/era-convert+to+policy.PNG)  
*Отриману конфігурацію перетворюємо до поліції*

Даний метод, не ідеальний так як не має ні коментарів що за пристрої мають цей серійний номер, а ні з якого вони комп'ютера.  Але вважаю скоро цей функціонал може з'явитися у самій програмі  "[ESET Remote Administrator](http://eset.ua/ua/products/for_business/remote__administrator/remote_administrator)" у наступних версіях.  
Ця публікація основана на версіях продуктів:  

* ESET Endpoint Security - 6.2.2033.1
* ESET Remote Administrator Server - 6.2.171.0
