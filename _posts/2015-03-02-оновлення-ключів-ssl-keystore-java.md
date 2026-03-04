---
layout: post
title: "Оновлення ключів SSL KeyStore (JAVA)"
date: 2015-03-02 23:24:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2015/03/ssl-keystore-java.html
---

Для керування [шифрованого з'єднання в програмі OpenMeeting](http://www.lexxai.pp.ua/2013/03/openmeeting-ssl.html) використовується інструмент [keytool.](http://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html)  
  
Створення приватного ключа  запиту для сервера сертифікації та встановлення описано у попередній [публікації](http://www.lexxai.pp.ua/2013/03/openmeeting-ssl.html).    
Пройшов час, рік,  потрібно встановити оновленні сертифікати.  
Перегляд усіх сертифікатів сховища з файлу /usr/lib/red5/conf/keystore.  

```
keytool -list -keystore  /usr/lib/red5/conf/keystore
```

Якщо використати існуючий приватний ключ, то просто генеруємо запит.  

```
keytool -certreq -keyalg RSA -alias red5 -file red5.csr \
  -keystore red5/conf/keystore
```

Відсилаємо запит до сервера сертифікації отримаємо відповідь у вигляді сертифікату lexxai.pp.ua.ca.crt для нашого ключа, та послідовність довірених ключів lexxai.pp.ua.ca-bundle у одному файлі.  
Я розбив lexxai.pp.ua.ca-bundle на два сертифікати, один для root інший для intermed.  
для оновлення я видалив попередні сертифікати   

```
keytool -alias root  -keystore /usr/lib/red5/conf/keystore -delete
```

```
keytool -alias intermed  -keystore /usr/lib/red5/conf/keystore -delete
```

Та імпортував нові   

```
keytool -import -alias root  -keystore /usr/lib/red5/conf/keystore \
  -trustcacerts -file ~/certificate/2015-2016/lexxai.pp.ua.ca-bundle.01
```

```
keytool -import -alias intermed  -keystore /usr/lib/red5/conf/keystore \
  -trustcacerts -file ~/certificate/2015-2016/lexxai.pp.ua.ca-bundle.02
```

А ось для пари приватний ключ-сертифікат у мене виникли проблеми, так як видалив alias red5 я видалю приватний ключ теж.  
А імпортування, не вийде так як такий аліас уже існує.  

```
/keytool -import -alias red5 -keystore /usr/lib/red5/conf/keystore -trustcacerts \
   -file ~/certificate/2015-2016/lexxai.pp.ua.ca.crt
```

Тому я експортую приватний ключ до зовнішнього файлу.  
Є варіанти з експорту приватного ключа до формату PKCS12 з яким можна працювати за допомогою OpenSSL:  

```
keytool -importkeystore -srckeystore /usr/lib/red5/conf/keystore -destkeystore \
  ~/certificate/2015-2016/exportkeystore.p12  -deststoretype PKCS12
```

  
Для імпорту назад:   

```
keytool -importkeystore -deststorepass ChangePassword -destkeypass ChangePassword \
  -destkeystore /usr/lib/red5/conf/keystore -srckeystore \
  ~/certificate/2015-2016/exportkeystore.p12 -srcstoretype PKCS12 \
  -srcstorepass ChangePassword -alias red5
```

Але я скористався іншим інструментом [KeyStore Explorer](http://keystore-explorer.sourceforge.net/) котрий може працювати безпосередньо з keystore.  
Експортував приватний ключ до файлу red5.pkcs8, видалив аліас 'red5'.  
А потім імпортував пару ключ плюс сертифікат як аліас 'red5'.  

[![](/assets/images/blog/3309da22fcd59e0f-0ecdc962c9257176.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh1nzKOuyc3ULS8HJWrqzvT_Y0ftCgoGn7GgYVKd233CJvCQtJSv5GkdbJPbT6PoQI0erDVqad41_3f1lC7cQLttw6YeWa2GaoTK2EzZY1hO23EkCU7BBUzYyCB_9ETlGeWyAKy4Cy0w2RY/s1600/keystore.png)

І таким чином в отримав новий keystore.  
  
P.S. Щоб не робити усього цього треба генерувати новий приватний ключ перед кожним продовженням.
