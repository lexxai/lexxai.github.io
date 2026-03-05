---
layout: post
title: "eToken PKSC11 створення та використання ключів у Ubuntu"
date: 2016-06-15 21:45:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2016/06/etoken-pksc11-ubuntu.html
---

["Колись працював](http://www.lexxai.pp.ua/search/label/token) з модулем  для зберігання ключів : Aladin eToken NG-OTP 72k (Java) (hardware token).  

[![](/assets/images/blog/a00cc35c7072de75-a8fbc8b811bbabbb.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEifoDGUdGi5WSYBC3wdR7kF7_Nh-_CAvyFQIc1MtyzEv_A7e0XAO3yWX2hcPar5JQYeko55E7VYrMHBdiXh58zT6jWY_Bm6_9Q3mRXGJGuDW-vNiUTN79EPvW8pl8A4nDYqEX_u56ZAlEq2/s1600/IMG_8686-s.jpg)  
*Aladin eToken NG-OTP 72k (Java)*

Вирішив підключити під ОС Ubuntu 16.04.  
  

### SafeNet Authentication Client

Так як зараз за  програмне забезпечення цих модулів відповідає компанія [SafeNet](http://www.safenet-inc.com/),  то вона має програмні клієнти та драйвери на кшталт "SafeNet Authentication Client".  Запитати у розробника ви можете за [посиланням](http://www.safenet-inc.com/multi-factor-authentication/security-applications/authentication-client-token-management/). Але воно розповсюджується не вільно для нових версій. А ось попередню версію "SafeNet Authentication Client" можна запитати і отримати у [аладіна](http://www.aladdin-rd.ru/support/request.php).  
Я запитав та отримав версію 9.1 для тестування.  
  

[![](/assets/images/blog/4f2c37e2c95e437a-56cd4114107d79fb.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgb6Vk2-Y2Bw_Jga6ah2hgfhgsO7fonKU9RtqZNCQbREneZna-vBRdIKgVAuzZzlFcAQtUQfI-cY5IiTin7hmIFQ3Xu0M4x6lKBOoPZtn_hRPw-9mrbW7hRQi1QAWLEIpyR86NrYWRgO1kc/s1600/safenetclient.png)  
*Authentication Client 9.1*

Завантажив файл SAC\_9\_1\_Linux.zip, видобув до теки SAC\_9\_1\_Linux.  

```
cd  ./SAC_9_1_Linux/Installation/Standard/DEB
cp install-portuguese_SafenetAuthenticationClient-9.1.7-0_amd64.deb.sh install-english_SafenetAuthenticationClient-9.1.7-0_amd64.deb.sh
vi install-english_SafenetAuthenticationClient-9.1.7-0_amd64.deb.sh
```

редагую зміну lang na ***lang=en-EN***
  

```
chmod +x install-english_SafenetAuthenticationClient-9.1.7-0_amd64.deb.sh
sudo ./install-english_SafenetAuthenticationClient-9.1.7-0_amd64.deb.sh
```

Після встановлення маємо бібліотеки для PKSC#11 /***usr/lib/libeTPkcs11***.so, та програми  "SafeNet Authentication Client", "SafeNet Authentication Client Tools".  

[![](/assets/images/blog/6cf8d04eb12894c9-b77a4d4aea0bc1f3.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg2OfRA7oPhoTX_7OyqFPK8lt7u-MwZj-BtrPtBejzVXnkHnq_WjANWAyoDNI86fj-7FKB8h7u6Jp2g-jYRn1TfY8ffqRX3OOn5QSVzNBKdganWpfomChETXn_NSJwP5oOwDjeMOsmGf83b/s1600/safenet-01.png)  
*SafeNet Authentication Client Tools*

### Mozila Thunderbird та Firefox

Токен можна використати у Mozila Thunderbird та Firefox якщо підключити модуль прописав шлях до бібліотеки.  

[![](/assets/images/blog/0e22bb75fd1353d8-552406748f959312.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhyxfkULXI1JGzCLTAbc7p-N-IGB00XkA0-Z8Is_JeHVpl4b202RKpchPHKKCu_IiX_3B2juPSGnGqCCjnWcrl_fMiTZ7xOL-pHWMGmODPBxTbNaAYC__Eci8wynUkrAVWMv-O89QmhGNCd/s1600/thb-etoken-01.png)  
*Шлях до бібліотеки*

  

[![](/assets/images/blog/60d1ac37e17f1f41-c3d5a5fb6be3cf6b.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi49tRhSGAmy3xnrrIIuGI04kJbJosRjFH0qmlaM2RDoNYTcXZyGeg8gqPpYwcZyFzFonUJLCX24mvhJ-GWyuwew7opbfvdh52Bldb0IK0aMnPVee6B5-HAPs2kPJUqozn6tOMlxLyPnJze/s1600/thb-etoken-02.png.png)  
*Інформація про токен*

 Для роботи з ключами є різні програм, наприклад програми що можуть імпортувати до ключа вже згенеровані приватні ключі, або згенерувати ключ виключно за допомогою токену, і ключ ніколи не вийде за межі токену, тільки публічний ключ може бути доступний.  
  

### Google Chrome

За цією інструкцією [CommonAccessCard - Community Help Wiki](https://help.ubuntu.com/community/CommonAccessCard):  

1. Встановлюю NSS tools для Debian/Ubuntu:

```
$ sudo apt-get install libnss3-tools
```

  

2. Додаю свій модуль "CAC Module" pkcs11 library

2b. Закрити браузер Chrome

3. Перевірити чи токен всталенно, чи ви в домашній теці, відкрити термінал:

```
$ cd ~
$ modutil -dbdir sql:.pki/nssdb/ -add "CAC Module Etoken" -libfile /usr/lib/libeTPkcs11.so
WARNING: Performing this operation while the browser is running could cause
corruption of your security databases. If the browser is currently running,
you should exit browser before continuing this operation. Type 
'q <enter>' to abort, or <enter> to continue: 

Module "CAC Module eToken" added to database.
```

  

4. Перевірити що бібліотека додана коректно:

```
$ modutil -dbdir sql:.pki/nssdb/ -list
```

  

5. Я маю результат на кшталт цього:

  

```
Listing of PKCS #11 Modules
-----------------------------------------------------------
  1. NSS Internal PKCS #11 Module
  slots: 2 slots attached
 status: loaded

  slot: NSS Internal Cryptographic Services
 token: NSS Generic Crypto Services

  slot: NSS User Private Key and Certificate Services
 token: NSS Certificate DB

  2. CAC Module eToken
 library name: /usr/lib/libeTPkcs11.so
  slots: 10 slots attached
 status: loaded

  slot: AKS ifdh 00 00
 token: eToken

  slot: 
 token: 

  slot: 
 token: 

  slot: 
 token: 

  slot: ETOKEN HID READER 0
 token: 

  slot: ETOKEN HID READER 1
 token: 

  slot: ETOKEN HID READER 2
 token: 

  slot: ETOKEN HID READER 3
 token: 

  slot: 
 token: 

  slot: 
 token: 
-----------------------------------------------------------
```

Тепер при налаштуваннях сертифікатів у бразуері Chrome, буде запити на пароль токену:  

[![](/assets/images/blog/49a3f818b47dcf57-0c82b2366773dd26.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiVZa3Y2MhuSeQgOOqkOTNznhmI9LUn4RcNQnxj0ksznV8Fl3Zh7IAS5hZzxbdnRr0wQ8QL7ecjJGSih7hJnZeiYODv98A6aCwrbZksUz4tHACNTSdY_4lkIjzWHiV3ikEkrh1E6fW-MI1a/s1600/etoken-chrome.png)  
*Google Chrome запит на токен пароль*

А після вводу паролю буде доступен сертифікат користовача у Google Chrome що зберігається у токені:  

[![](/assets/images/blog/c79a1ea23a13df31-1c5e425c2320f530.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjcqDazd-tJlKPk2HPQFX_QI50Pe1nQRvD7K0xnS3cZ73AIlm7p8qoF1X25hbu3JOPb39VQU9BEsGl6AuzSs6OqC9HkRu_ad-L3u7ofcaaz6KeHz3Vy-XG-iQ17tOTTD9ypBVvAWSndkhM2/s1600/etoken-chrome-1.png)  
*cертифікат користовача у Google Chrome що зберігається у токені*

  

### p11tool

```
$ p11tool
Програма 'p11tool' наразі не встановлена. Ви можете встановити її набравши таке:
sudo apt install gnutls-bin
$ sudo apt install gnutls-bin
p11tool --list-tokens --provider=/usr/lib/libeTPkcs11.so
Token 0:
    URL: pkcs11:model=eToken;manufacturer=SafeNet%2c%20Inc.;serial=XXXXXX;token=eToken
    Label: eToken
    Type: Hardware token
    Manufacturer: SafeNet, Inc.
    Model: eToken
    Serial: XXXXXX
    Module: (null)
```

```
p11tool  --provider=/usr/lib/libeTPkcs11.so  --generate-rsa --bits 2048 --label "MyNewKey" --login --outfile MyNewKey.pub "pkcs11:model=eToken;manufacturer=SafeNet%2c%20Inc.;serial=XXXXXX;token=eToken"
```

```
Token 'eToken' with URL 'pkcs11:model=eToken;manufacturer=SafeNet%2c%20Inc.;serial=XXXXXX;token=eToken' requires user PIN
Enter PIN:  
```

Вводимо пін від токену, додатковий RSA пароль (в залежності від налаштувань ключа), після деякої затримки - генерації ключа, створено буле файл MyNewKey.pub котрий містить публічний ключ.  
  
Надалі повинно спрацювати використання приватного ключа, але у мене не спрацювало:
  

```
certtool --provider=/usr/lib/libeTPkcs11.so  --generate-request --load-privkey "pkcs11:model=eToken;manufacturer=SafeNet%2c%20Inc.;serial=XXXXXX;token=eToken" --load-pubkey MyNewKey.pub --outfile request.pem
```

  

### pkcs11

```
$ pkcs11-tokens -m /usr/lib/libeTPkcs11.so

arning: PKCS#11 provider has no digest service
This HSM will not work with BIND 9 using native PKCS#11.

DEFAULTS
    rand_token=0x55f5ff4837e0
    best_rsa_token=0x55f5ff4837e0
    best_dsa_token=(nil)
    best_dh_token=(nil)
    digest_token=(nil)
    best_ec_token=(nil)
    best_gost_token=(nil)
    aes_token=(nil)

TOKEN
    address=0x55f5ff4837e0
    slotID=0
    label=eToken                          
    manufacturerID=SafeNet, Inc.                   
    model=eToken          
    serialNumber=XXXXXXX       
    supported operations=0x6 (RAND,RSA) 
```

```
$ pkcs11-keygen -m /usr/lib/libeTPkcs11.so -l myKK -b 2048

Enter Pin: 
Warning: PKCS#11 provider has no digest service
This HSM will not work with BIND 9 using native PKCS#11.

(pkcs11-keygen:8625): Gtk-WARNING **: /build/gtk+2.0-KsZKkB/gtk+2.0-2.24.30/gtk/gtkwidget.c:10000: widget class `GtkProgressBar' has no property named `visible'
Key pair generation complete. 
```

```
$ pkcs11-list -m /usr/lib/libeTPkcs11.so
Enter Pin: 
Warning: PKCS#11 provider has no digest service
This HSM will not work with BIND 9 using native PKCS#11.
object[0]: .....

object[6]: handle 128647179 class 3 label[4] 'myKK' id[0] 
object[7]: handle 32636940 class 2 label[4] 'myKK' id[0]
```

Далі буде...
