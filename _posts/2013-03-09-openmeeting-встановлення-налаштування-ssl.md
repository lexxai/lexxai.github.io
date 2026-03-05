---
layout: post
title: "Openmeeting, встановлення, налаштування SSL"
date: 2013-03-09 18:33:00 +0000
tags: ["internet", "multimedia", "net", "Openmmeting", "SSL"]
blogger_orig_link: https://lexxai.blogspot.com/2013/03/openmeeting-ssl.html
---

[Openmeetings](http://openmeetings.apache.org/) забезпечує проведення відеоконференції, обмін миттєвими повідомленнями, спільна дошка для малювання, показ робочого столу,спільне редагування документів та інших засобів групового використання API функцій Red5 Streaming Server для віддаленої взаємодії і потокового відео.  
  
[Інструкція з встановлення](http://openmeetings.apache.org/installation.html) складається з трьох етапів.  
  
![](/assets/images/blog/2faeef320f9fad0a-c9c1e1fb3ee2cd0e.png)  

Завантажити, розпакувати, запустити скрипт red5, перейти до інтерфейсу web-встановлення.  
  
Я завантажу останню версію - остання версія що у розробці:  
https://builds.apache.org/view/M-R/view/OpenMeetings/job/openmeetings/lastStableBuild  
  

```
cd ~
wget "https://builds.apache.org/view/M-R/view/OpenMeetings/job/openmeetings/lastStableBuild/artifact/singlewebapp/dist/apache-openmeetings-2.1.0.r1450001-26-02-2013_0309.tar.gz"
```

  

```
mkdir red5
tar -C red5 -xzf apache-openmeetings-2.1.0.r1450001-26-02-2013_0309.tar.gz
```

  
Використовувані в команді ключі  

* x - дозволяє вам витягувати файли з архіву.
* f - є обов'язковою опцією. Без неї tar намагається використовувати магнітну стрічку замість файлу архіву.
* z - дозволяє вам обробляти архів, стиснутий архіватором gzip (з розширенням. gz). Якщо ви забудете вказати цю опцію, tar видасть помилку. І навпаки, ця опція не повинна використовуватися для не стислих архівів.
* v - робить висновок tar докладним. Це означає, що на екран будуть виведені всі знайдені в архіві файли. Якщо ця опція опущена, інформація, що виводиться в процесі обробки, буде обмежена.

```
mv red5 /usr/lib
cd /usr/lib/red5
```

  
Якщо ви оновлюєте попередню версію то можна зробити збереження попередніх налаштувань  

```
admin -b -file ~/backup_today.zip
```

Відновити налаштування можна так
  

```
admin -r -file ~/backup_yesterday.zip
```

А ось так можна почати встановлення разом з відновленням
  

```
admin -i -file ~/backup_yesterday.zip
Setting default logging context: default 
Context init...
Logger name for context: openmeetings
Context logger config file: logback-config.xml
....
lorer item import complete, clearing temp files
... Done
```

  
У моєму завданні було встановити шифроване з'єднання при використанні OpenMeetings з Adobe Flash Player з використанням протоколу [RTMPS](http://openmeetings.apache.org/RTMPSAndHTTPS.html).  
  
[Налаштування RTMPS](http://openmeetings.apache.org/RTMPSAndHTTPS.html)

1. Налаштування RTMPS для використання його у Adobe Flash Client.  
  
Створю нове сховище ключів (тека red5/conf/keystore) та ключ, використовуючи один той самий пароль як для ключа так і для сховища.  

```
keytool -keysize 2048 -genkey -alias red5 -keyalg RSA -keystore red5/conf/keystore
Enter keystore password: вводимо пароль для сховища ключів
Re-enter new password: повторюємо його, та не забуваємо
What is your first and last name?
 [Unknown]: вводимо назву вашого домену, наприклад: lexxai.pp.ua
What is the name of your organizational unit?
 [Unknown]: вводимо назву вашого підрозділу, наприклад: IT
What is the name of your organization?
[Unknown]: вводимо назву вашої організації, наприклад: Maemo sho mayemo
What is the name of your City or Locality?
 [Unknown]: вводимо назву вашого міста, наприклад: Kyiv
What is the name of your State or Province?
 [Unknown]: вводимо назву вашого регіону, наприклад: Kyivska
What is the two-letter country code for this unit?
 [Unknown]: вводимо назву вашої країни двома літерами, наприклад: UA
Is CN=lexxai.pp.ua, OU=IT, O=Maemo sho mayemo, L=Kyiv, ST=Kyivska, C=UA correct?
[no]:відповідаємо yes, для збереження якщо усе вірно
Enter key password for <red5>
        (RETURN if same as keystore password): призначаємо для ключа той самий пароль що і для сховища, натискаючи ENTER на клавіатурі
```

Надалі, формую запит для отримання сертифіката у кореневого центру сертифікації (файл red5.csr).  

```
keytool -certreq -keyalg RSA -alias red5 -file red5.csr -keystore red5/conf/keystore
Enter keystore password:
```

Отриманий файл запиту для отримання сертифікату можна перевірити перед тим як купувати, наприклад на сайті видачі сертифікатів COMODO.  

[![](/assets/images/blog/9aa131725ee88827-6427561bab3d4406.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiu8513Rk6l6gfSPSZUf12iMzBIv5T2AHVjKGzLkirHxzP_8TaO_I5fzd7PtRoBaFKLX8C7H4Pz64RviaeiBCy9kQSkoHm6tRpjGOErTZpVdT-xDw1CJ5WHEDxrAN8DG6_798u3WhcEjDra/s1600/om-comodo-01.png)  
*Перевіримо запит на сертифікат на сайті https://comodosslstore.com/checkcsr.aspx*

Якщо ви купили сертифікат, або використовували само підписаний сертифікат від свого кореневого центру сертифікації, ви повні отримати сертифікат, ті ланцюжок сертифікатів з проміжних та кореневих центрів сертифікації, що вповноважують їх автентичність. У моєму випадку сертифікати спочатку були протестовані на само підписаному сертифікаті, а потім були придбанні на центрі сертифікації COMODO.  
Я отримав кореневий сертифікат центру сертифікації AddTrustExternalCARoot.crt, проміжний сертифікат центру сертифікації PositiveSSLCA2.crt, та файл www.lexxai.pp.ua.crt з сертифікатом  згідно закритого ключа за запитом з файлу red5.csr.  
Імпортую кореневий сертифікат центру сертифікації:
  

```
keytool -import -alias root -keystore red5/conf/keystore -trustcacerts -file AddTrustExternalCARoot.crt
Enter keystore password:
Certificate already exists in system-wide CA keystore under alias <addtrustexternalca>
Do you still want to add it to your own keystore? [no]:  yes
Certificate was added to keystore
```

Імпортую проміжний сертифікат центру сертифікації:
  

```
keytool -import -alias intermed -keystore red5/conf/keystore -trustcacerts -file PositiveSSLCA2.crt
Enter keystore password:
Certificate was added to keystore
```

Імпортую сертифікат закритого ключа:
  

```
keytool -import -alias red5 -keystore red5/conf/keystore -trustcacerts -file www.lexxai.pp.ua.crt
Enter keystore password:
Certificate reply was installed in keystore
```

Перевіримо імпортовані ключі з сховища ключів:
  

```
keytool -list -keystore red5/conf/keystore
Enter keystore password:
Keystore type: JKS
Keystore provider: SUN
Your keystore contains 3 entries
root, Feb 28, 2013, trustedCertEntry,
Certificate fingerprint (MD5): 1D:.........................................:3F
red5, Feb 28, 2013, PrivateKeyEntry,
Certificate fingerprint (MD5): E5:.........................................:6C
intermed, Feb 28, 2013, trustedCertEntry,
Certificate fingerprint (MD5): 0C:.........................................:33
```

2. Налаштовую використання протоколу RTMPS у RED5  
Розкоментував розділ <!-- RTMPS --> у файлі red5/conf/red5-core.xml  

```
   <!-- RTMPS -->
    <bean id="rtmpsMinaIoHandler" class="org.red5.server.net.rtmps.RTMPSMinaIoHandler">
        <property name="handler" ref="rtmpHandler" />
        <property name="rtmpConnManager" ref="rtmpMinaConnManager" />
        <property name="keyStorePassword" value="${rtmps.keystorepass}" />
        <property name="keystoreFile" value="conf/keystore" />
    </bean>

    <bean id="rtmpsTransport" class="org.red5.server.net.rtmp.RTMPMinaTransport" init-method="start" destroy-method="stop">
        <property name="ioHandler" ref="rtmpsMinaIoHandler" />
        <property name="connectors">
            <list>
                <bean class="java.net.InetSocketAddress">
                    <constructor-arg index="0" type="java.lang.String" value="${rtmps.host}" />..
                    <constructor-arg index="1" type="int" value="${rtmps.port}" />
                </bean>
            </list>
        </property>
        <property name="ioThreads" value="${rtmp.io_threads}" />
        <property name="tcpNoDelay" value="${rtmp.tcp_nodelay}" />
    </bean>
```

Редагуємо файл red5/conf/red5.properties та встановлюю наступні значення  

```
rtmps.port=5443
rtmps.keystorepass=password (password = password you set on your new keystore)
```

Редагуємо файл red5/webapps/openmeetings/public/config.xml та встановлюю наступні значення
  

```
<rtmpsslport>5443</rtmpsslport>
<useSSL>yes</useSSL>
<proxyType>best</proxyType>
```

Перезавантажуємо  red5 та спробуємо підключитися Ваше з'єднання мусить використовувати RTMPS порт 5443, можете перевірити програмами моніторингу поточних мереживних з'єднань.  
  
Продовження: [оновлення сертифікатів](http://www.lexxai.pp.ua/2015/03/ssl-keystore-java.html), [налаштування HTTPS](http://www.lexxai.pp.ua/2016/01/openmeeting-https.html)
