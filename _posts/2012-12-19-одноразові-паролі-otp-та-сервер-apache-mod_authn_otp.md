---
layout: post
title: "Одноразові паролі (OTP) та сервер Apache (mod_authn_otp)"
date: 2012-12-19 21:30:00 +0000
tags: ["aladdin", "apache", "HMAC", "HOTP", "mobile-otp", "mod_authn_otp", "One Time Password", "otp", "RFC 4226", "security", "token"]
blogger_orig_link: https://lexxai.blogspot.com/2012/12/otp-apache-modauthnotp.html
---

Одноразові паролі ([One Time Password](http://en.wikipedia.org/wiki/One-time_password) - OTP) надають можливість підвищити рівень безпеки при доступі до певних веб сторінок з використанням традиційних паролів і ввести [двох факторний рівень автентифікації](http://en.wikipedia.org/wiki/Two-factor_authentication). Тобто якщо ви використали публічне місце для доступу, наприклад до корпоративної web пошти, є ймовірність запису паролів що були Вамі набранні третьою стороною. Використання одноразового паролю не дасть використовувати одноразовий пароль повторно і Ваш набранний пароль до корпоративної пошти не матиме значення без правильного одноразового.   
Генерація одноразових паролів може бути зробленна багатьма алгоритмами та пристроями, або комбінацією пристроїів та алгоритмів.  
В даній публікації використовую пристрій для генерації одноразових паролів за алгоритмом [HMAC-based](http://en.wikipedia.org/wiki/HMAC) [One Time Password](http://en.wikipedia.org/wiki/One-time_password) (HOTP) що описаний специфікацією [IETF](http://en.wikipedia.org/wiki/IETF) [RFC 4226](http://tools.ietf.org/html/rfc4226). У моєму випадку це токен "[Aladdin eToken NG-OTP](http://www.aladdin-rd.ru/catalog/etoken/otp/)".  
  

[![](/assets/images/blog/67e05fef9f02ce4d-a8fbc8b811bbabbb.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhfodAyYxzSPNE8KWKqZ_CYqdsAEjtl2ir4o3qfqb3gEGUwHrFrlHldAQGmV9q2PZWR72cOJ6MHhd6wfV6IdvFiowfwHT3iiMHdRqZlCxB4BNN0_F4M3HEAeWDnuXwGiDjZ_9Fg-8kq5IoX/s1600/IMG_8686-s.jpg)  
*"Aladdin eToken NG-OTP", у режимі генерації HOTP.*

А також можна використовувати [інші пристрої](http://code.google.com/p/mod-authn-otp/wiki/Tokens) з іншими алгоритмами генерації одноразових паролів, наприклад, [Mobile-OTP](http://motp.sourceforge.net/).  
Розгляну на прикладі варіанти практичної реалізації для HOTP та Mobile-OTP.  
  
  
  

### Серверна частина

Налаштовую серверну частину. Використовується сервер [FreeBSD 9.0](http://www.freebsd.org/), www сервер "[apache-2.2.21](http://www.apache.org/)".  
Для розширення функціоналу www сервера і можливості використовувати автентифікацію одноразовими паролями необхідно встановити додатковий модуль "[mod\_authn\_otp](http://code.google.com/p/mod-authn-otp/)".  
Модуль "[mod\_authn\_otp](http://code.google.com/p/mod-authn-otp/)" для сервера "Аpache" встановлюю з портів:   
Port:   ap22-mod\_authn\_otp-1.1.4\_1  
Path:   /usr/ports/www/mod\_authn\_otp  
Info:   Apache module for one-time password authentication  

*#cd /usr/ports/www/mod\_authn\_otp; make instal clean;*  
  
В процесі встановлення буде додано модуль"mod\_authn\_otp.so" сервера "Apache", а також інструмент "[otptool](http://code.google.com/p/mod-authn-otp/wiki/OTPTool)" - HOTP/OATH one-time password utility.  
Після встановлення, до конфігураційного файлу сервера "Apache" - "httpd.conf", буде додано закоментований рядок з ввімкненням модуля "mod\_authn\_otp.so", після заняття коментування отримав :  
"LoadModule authn\_otp\_module   libexec/apache22/mod\_authn\_otp.so"  
  
Надалі знаходжу розділ керування розділом сайту що треба захистити, наприклад  
"/usr/local/www/postfixadmin/". Та додаю інформацію про захист цього розділу одноразовим паролем:  
  
 <Directory "/usr/local/www/postfixadmin/">  
    AuthType basic  
    AuthName "Area protected by OTP"  
    AuthBasicProvider OTP  
    OTPAuthUsersFile "/usr/local/etc/apache22/otp/users.otp"  
    Require valid-user  
  </Directory>  
  
Згідно цього налаштування, файл конфігурації з даними про користувачів, директива OTPAuthUsersFile, знаходиться у теці "/usr/local/etc/apache22/otp". Створимо теку, і надамо права запису процесом "Apache" від імені користувача "www" до неї. Також створимо файл в цій теці з даними про користувачів - "users.otp".  
  
*#mkdir  /usr/local/etc/apache22/otp*  
*#chmod 700 /usr/local/etc/apache22/otp*  
*#touch  /usr/local/etc/apache22/otp/users.otp*  
*#chown -R www:www  /usr/local/etc/apache22/otp*  
*#chmod -R 600 /usr/local/etc/apache22/otp*  
  
Структуру файлу конфігурації описано тут <http://code.google.com/p/mod-authn-otp/wiki/UsersFile>.  
  
# Fields:   
#   1. Token Type         See below  
#   2. Username           User's username  
#   3. PIN                User's PIN, or "-" if user has no PIN, or "+" to verify PIN via "OTPAuthPINAuthProvider"  
#   4. Token Key          Secret key for the token algorithm (see RFC 4226)  
#   5. Counter/Offset     Next expected counter value (event tokens) or counter offset (time tokens)  
#   6. Failure counter    Number of consecutive wrong OTP's provided by this users (for "OTPAuthMaxOTPFailure")  
#   7. Last OTP           The previous successfully used one-time password  
#   8. Time of Last OTP   Local timestamp when the last OTP was generated (in the form 2009-06-12T17:52:32L)  
#   9. Last IP address    IP address used during the most recent successful attempt  
#  
#   Fields 5 and beyond are optional. Fields 6 and beyond should be omitted for new users.  
  
# Token Type Field:  
#  
# This field contains a string in the format: ALGORITHM [ / COUNTERINFO [ / DIGITS ] ]  
#  
# The ALGORITHM is either "HOTP" (RFC 4226) or "MOTP" (http://motp.sourceforge.net/).  
#  
# The COUNTERINFO is either "E" for an event-based token, or "TNN" for a time based token  
# where "NN" is the number of seconds in one time interval. For HOTP, the default is "E";  
# for MOTP, the default is "T10".  
#  
# The DIGITS is the number of digits in the one-time password; the default is six.  
#  
# Examples:  
#  
# HOTP - HOTP event-based token with six digit OTP  
# HOTP/E - HOTP event-based token with six digit OTP  
# HOTP/E/8 - HOTP event-based token with eight digit OTP  
# HOTP/T30 - HOTP time-based token with 30 second interval and six digit OTP  
# HOTP/T60 - HOTP time-based token with 60 second interval and six digit OTP  
# HOTP/T60/5 - HOTP time-based token with 60 second interval and five digit OTP  
# MOTP - Mobile-OTP time-based token 10 second interval and six digit OTP  
# MOTP/E - Mobile-OTP event-based token with six digit OTP  
  
Для тестування я створив наступну конфігурацію з даними про користувачів - "users.otp":   
  
HOTP test-h -    a19cdbe8475ccca8cae2d31c798b96b6e2d821f10f0d582f 0  0  
MOTP test-m 1111 074581dae926835a 0  0  
  
Де перший рядок, для автентифікації користувача "test-h" з використанням пристрою "[Aladdin eToken NG-OTP](http://www.aladdin-rd.ru/catalog/etoken/otp/)".  
Де другий рядок, для автентифікації користувача "test-m" з використанням програмної реалізації "[Mobile-OTP](http://motp.sourceforge.net/)".  
  

### Клієнтська частина для HOTP

Для конфігурації користувача "test-h", нам потрібен секретний ключ для токену. У пристроях на кшталт "[Aladdin eToken PASS](http://www.aladdin-rd.ru/catalog/etoken/pass/)" він фіксований для кожного пристрою, і надається при продажу у вигляді файлу. У "[Aladdin eToken NG-OTP](http://www.aladdin-rd.ru/catalog/etoken/otp/)" він можу бути змінним. Для ініціалізації  існує програмний комплекс [SafeNet Authentication Manager (SAM)](http://www.aladdin-rd.ru/catalog/sam/) - система призначена для впровадження, управління, використання та обліку апаратних засобів аутентифікації користувачів в масштабах підприємства. Продається окремо.  
Але для моїх потреб, цей комплекс не потрібен. Після консультації зі службою підтримки компанії "Aladdin", з'ясував що це можна зробити за допомогою [SDK](http://www.aladdin-rd.ru/support/downloads/get?ID=3929&sphrase_id=105076). Тому я зробив [запит на отримання SDK](http://www.aladdin-rd.ru/support/sdk.php?sphrase_id=105076) і отримав його. На основі наданих у [SDK](http://www.aladdin-rd.ru/support/downloads/get?ID=3929&sphrase_id=105076) прикладів, створив свою програму для ініціалізації об'єкту OTP у токені - "initOTP.exe".  
Після отримання дозволу на публікацію програми, я її викладу до публічного доступу.  
Використовувати її треба так:   
InitOTP <user-password> [[init counter] [hex secure key string]]   

Examples:

Random key usage: InitOTP 1234567890   
  Init count passw: InitOTP 1234567890 55

Fixed key usage : InitOTP 1234567890 0 b09cdbe8475ecca8ca22d31c798b96b6e2d831f10f0d581f

Де user-password - пароль доступу до eToken,init counter - початковий номер пароля OTP (0 - по замовчуванню), hex secure key string - бажаний ключ, якщо його не вказувати буде його згенеровано автоматично.   
Результатом роботи буде створено новий об'єкт OTP, з заданим ключем або ключем згенерованим автоматично, з визначеним початковим порядковим номером паролю OTP. Надалі програма запросить натиснути клавішу генерації одноразового паролю (на токені) і таким чином перевірить якість згенерованого пароля. По закінченню перевірки видасть ключ у шістнадцятирічному форматі довжиною 24 байти (192 біти), котрий треба зберегти і використовувати у програмах на стороні сервера, наприклад у файлі - "users.otp" для "mod\_authn\_otp". Після чого токен готовий до використання і його можна витягнути з роз'єму USB.  
  
До уваги, після ініціалізації токену (Initialize Token) засобами PKI, наприклад "SafeNet Authentication Client", об'єкт OTP з токену видаляється, і при спробі запиту на пароль OTP на дисплеї буде показано помилку "Err 14". З цієї помилки у мене і почалася робота над даною публікацією.  
  

### 

### Клієнтська частина для Mobile-OTP

Для конфігурації користувача "test-m", нам потрібен секретний ключ що використовується у програмі [Mobile-OTP](http://motp.sourceforge.net/), ця ж програма його і може генерувати.  
  
Я наведу приклад використання [Mobile-OTP для ОС Android](https://play.google.com/store/apps/details?id=org.cry.otp&hl=uk):   

[![](/assets/images/blog/6784d93b7c4656e5-d5931130a4ea6586.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhBQrfnPsLmLfqa_whgsDKMSTvSu_M1vN0ENBQqEFzTPslHidCIj4qVjnov1Nlq_vU9vZLsXIYEaM-djK9NEtEe4UiZ8OD_Bw4K39IedLacQA3qgdb4aXI1-IYUSrtg0s7YJQqmG4aq1Pg0/s1600/IMG_20121219_193256.jpg)  
*Android 4.1, Play Market, програма Mobile OTP*

[![](/assets/images/blog/a38d301474b7b506-17f997d5a6db813a.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiFRD7RxlrFIVrpAkiwD8iUr2MQw7T5snXLRhFiqMzog_X1elo3an1gksWQqzQvJH7jr7ZyPdAPiplys-zpwpwRS571l0IK-R9JGtNw0ovoBMTraegqRLeOzhN_RcymI_kHH2s0gaEaGf2o/s1600/IMG_20121219_193350.jpg)  
*Створюємо профіль, і вибираємо метод OTP*

[![](/assets/images/blog/750979230ded2b5f-1332bef0ec439e5b.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg3DyYnJq1s-X15wjRMQeKV7EwAcfRKuJfnNfVnQTX2g6a5tONVLNP0N9u1x43U9W6a4pTZAeW5I9m-JmUTogFoMJq70HAncx9o81uRruhgTcLfAAdZBZozEvxl5q-9SHSDHrir4tvxnJK2/s1600/IMG_20121219_193939.jpg)  
*вибираю метод HOTP, для симуляції роботи як у eToken OTPseed - ключ токену.*

[![](/assets/images/blog/bfa7776559726226-21364c656572c5ef.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg2b7n_zKDskOaM8DgtWULTL3_lWfi51Enoy_j-tp8HxFG0vxDI0PS7PSyt4MM6GJCXzbAxRXzt7QjOE2NCvC3zefxO27Uy9G2Vi4gYXl6JwUtAdXIBQSCjVmELQt4_Z-5pvuE-VpaAyWN6/s1600/IMG_20121219_194312.jpg)  
*вибираю метод mOTP, seed - випадкові 20 знаків*

[![](/assets/images/blog/2ea8773917dced9c-d5eff3c6e62eaa7c.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjqEND6qiCwpqH4Nk_quc5elvnPqp2TqSMmeZrvHkFXOJWfPMbj7Y-N5S74ErXgWGjj-kxQ66U2LxE-QrYZcCuqAvijF7T2NI6IGme-mUzDKHonJhef1H9lZH8VW06bwyef0TK-f-vLtU7D/s1600/IMG_20121219_194620.jpg)  
*профіль mobile-otp готовий до використання,потрібно ввести PIN, отримаю пароль*

[![](/assets/images/blog/e88e5f323eda7aba-ecf7d8c4087daec7.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjyO7IvWUPaD3ocpAoytyhFmosuWeZ4YrPXLuxjhrXQJjCZTSYXspwbpTZLbNcYmZiSLRh8oF_oljZ_-2RUOW5zYb3InEjGlY5WlhDwVRnQCfbLtnr8a0gn1eiPIv2xFBCQS4PiZaY1X2qF/s1600/IMG_20121219_194644.jpg)  
*вибір налаштованих профілів*

[![](/assets/images/blog/6ce63855f3cc1813-d761db26d65f42c0.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgoJAsX1HNe9g7LpmS4REy3_zpxFD0vLNMontVfGwcZSJ1tM5a0KMMAvApDNr0x5Sgf7myZ6cwM0Aj-kt8kHmDi3HoX4GfI04XLF2eMBpE6c6i5RrwVE4w030yNYaZPcWkgUEzC6659wsqz/s1600/IMG_20121219_194657.jpg)  
*Якщо довго натиснути на назву профілю то отримаємо менюдля показу секретного коду що потрібен для users.otp модуля mod\_authn\_otp.*

Приклад використання [Mobile-OTP , Java MIDlet](http://motp.sourceforge.net/#2):  
  

[![](/assets/images/blog/bc605f44516399e4-954ec04cfda31606.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEinoM6EjAiIKxZold5vHeJCdaEAma6v-Eo9xQwTIB6QCFQCzEgnYnyKUbYvx63w7p8xZ-klLoHnMPrrYAcCPT1WKhtSdTR79x4zZLPwmZzSLMZ3DsCifTMvy8VE0s8EAydfUb66p3EsDFhE/s1600/IMG_8680-s.jpg)[![](/assets/images/blog/93b46d2b89a8271f-9b3fe3ffc6cc24fb.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiZA8rwHeZW3ZkhstlKXMo74kLOhXTFpeo8zwrittwUr3WGgJm5n1qpd32B-nUcuZqqEj71tC_9c5PeaA5xfiZVPjZnRUDuJutX-9wjHQR1ZdfD0WwYSwOwSOLGDhjenHWknLLDXZ7L8r1d/s1600/IMG_8685-s.jpg)[![](/assets/images/blog/73d5a1c06579cb7d-2faf7020213887ec.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhWCSd0wCXyq_HgozDrps9e-Z5Yx5y5Al1OhfiAW5lsz4-k_6EN1BfbpxNo9MZOcdaYT2LPTGl4t81Z2h8pt3opPddrRvaz4qrrzOfUvFX2aeDxl4ksS315I12ulDQndIRDVrx79ge3OyNQ/s1600/IMG_8681-s.jpg)

[![](/assets/images/blog/b80d300ff4f4460b-f776da499cab0b47.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi5zzeITzoUXi9QVgIE5WU5j09YprHPjf0W80AaMZWffZIBL7qntyJ0cygR6hB1EtVaOSy3QywrnSjvP_Mg6CV6PFIbdVQIDhguZk-OeunfeHw9IeP4T_ES8HSG_9Z-sAG0TeUmHKGOniCK/s1600/IMG_8682-s.jpg)[![](/assets/images/blog/57893d7dc87f4701-8e2564d337e14b53.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhoGoVtj0agpyGf8B5QIxtFyiIP8T4llz5hkxcyXr3eEYMaByf0Pn6WcGnGg9p1RdQXFuFqySOmo1wLfLpJ0y_Bfq1ElFMp2ENbCz9c0uGsOo1v7X12fB7RY6amLr91ShlpV4mVQ5OHfx6P/s1600/IMG_8683-s.jpg)[![](/assets/images/blog/b14b36a1a633e7a3-d947b4d4a7bf8713.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhzCRHGxwEZ-d9CpWF_UYsnLBKr2nQsDgRFvUL7tIC5MqRxpWYTXrh_TfP3z-7qnqGTJvufsLSu-K9vJJ-6TXsOgrVDCBH0q6HTUNZe0Puv32lbX3npxG4eLFM_j6gA-oVpEvoaQpvdrSnl/s1600/IMG_8684-s.jpg)

  
Якщо ввести PIN:0000, то перейдемо до режиму ініціалізації, потрібно ввести 25 випадкових цифр, і отримаємо Init-Secret що потрібен для users.otp модуля mod\_authn\_otp. У опціях є можливість переглянути поточний відбиток часу Epoch-Time, часовий пояс і за необхідністю можна його змінити (клавіші 1 та 3).   
  
З секретними паролями закінчено, тепер потрібно синхронізувати пристрої.  
  

### Синхронізація OTP

Для коректного функціювання режиму "Mobile-OTP" треба перевірити синхронізацію часу на клієнті та на сервері. Отримаємо час на сервері: #date +%s : 1355944678. Беремо перші 9 знаків з отриманого часу віднімаємо від значення "Epoch-Time" що бачимо у програмі "Mobile-OTP". Різницею буде час у секундах, вона повинна бути не більш ніж 10 секунд. Якщо вона за велика і кратна 3600, то потрібно пропорційно змінити часовий пояс у програмі "Mobile-OTP" і перевірити відповідність "Epoch-Time". Або синхронізувати час на мобільному пристрою та сервері за [NTP](http://uk.wikipedia.org/wiki/NTP).  
  
У файлі конфігурації користувача присутній 5й параметр що відповідає за значення лічильника одноразового паролю або корекція часу клієнта на основі останнього прийнятого паролю.   
  
Для  режиму "HOTP".  Якщо у Вас є тільки що проініціалізований токен, то ви можете знати значення лічильника одноразового паролю і прописати його значення у 5му параметрі файлу конфігурації користувача.  
У випадку якщо ви його не знаєте значення лічильника одноразового пароля то його треба з'ясувати і записати це значення у 5му параметрі.  
  
Є декілька методів:  

1. Використати дописану мною [програму](https://docs.google.com/open?id=0ByJHIt7DSY3cNUNoaXUtS2UtajQ) на PHP, на основі [цієї публікації](http://habrahabr.ru/post/105268). Програма запитує у користувача два послідовно отриманих від токену паролі, знаючи ключ токену (прописаний у тілі програми), методом перебору визначає який поточний номер паролю. Ця програм також може використовується для автентифікації введеного одноразового паролю.
2. Використати функцію модуля "[mod\_authn\_otp](http://code.google.com/p/mod-authn-otp/)" - автосинхронізацію. У [параметрах "mod\_authn\_otp"](http://code.google.com/p/mod-authn-otp/wiki/Configuration) є директива "OTPAuthMaxOffset", по замовчуванні це значення 4, що дозволяє підкорегувати значення лічильника паролів на стороні сервера якщо користувач натискав кнопку генерування пароля на токені, але не вводив їх під час автентифікації до сервера. Якщо тимчасово прописати директиву "OTPAuthMaxOffset" у файлі httpd.conf розділ потрібної теки :<Directory> з великим значенням наприклад 5000, то "mod\_authn\_otp" буде робити спробу коригування і сам визначить і запам'ятає наступне значення лічильника. Після цієй опреції директиву  "OTPAuthMaxOffset" треба повернути у попередній стан.
3. Використати інструмент [otptool](http://code.google.com/p/mod-authn-otp/wiki/OTPTool) , що є складовою частиною "[mod\_authn\_otp](http://code.google.com/p/mod-authn-otp/)"  
   Usage: otptool [-fht] [-c counter] [-d digits] [-i interval] [-m PIN] [-w window] key [otp]  
   Options:  
    -c Specify the initial counter value (conflicts with `-t')  
    -f `key' refers to the file containing the key  
    -h Display this usage message  
    -i Specify time interval in seconds (default 30)  
    -m Use mOTP algorithm with given PIN; also implies `-d 6' and `-i 10'  
    -t Derive initial counter value from the current time (conflicts with `-c')  
    -n Specify number of digits in the generated OTP(s) (default 6)  
    -w Specify size of window for additional counter values (default 0)  
     
   #otptool -w 5000 a19cdbe8475ccca8cae2d31c798b96b6e2d821f10f0d582f 314090   
   592  
   Результат, номер для пароля "314090" є 592

### Тестування сервера

Після налаштувань файлу конфігурації сервера "Apache" - httpd.conf, та файлу конфігурації користувача модуля "[mod\_authn\_otp](http://code.google.com/p/mod-authn-otp/)" - "/usr/local/etc/apache22/otp/users.otp", необхідно перезавантажити службу сервера "Apache". I спробувати зайти на ресурс сервера що було захищено. Ось приклад запиту на автентифікацію.  
  

[![](/assets/images/blog/3ea46678425372c4-d70c1a8d0d520063.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiCPX4TMqNB0KFVwatLXieHZjX_N62-RG8aXgAV7rgpC6i9smaNGMAhTTr89n2oifVhz6YuwR9ThK9H9CnWlLL1XfP4MHJYu2LPolmYwDGo46OT1slvVRa6HJaaYjqUDe6Fp5rNHd-H72Zt/s1600/otp-opera.PNG)  
*Запит автентифікації для введення паролю OTP*

Якщо на дисплеї ви бачите 500 помилку сервера, замість запиту автентифікації - зверніть увагу на файл протоколу помилок "apache", можливо потрібно звернути увагу на права доступу до теки з даними користувачів, так як модуль "[mod\_authn\_otp](http://code.google.com/p/mod-authn-otp/)" повинен мати права запису та створення.  
  
Після першої коректної автентифікації файл конфігурації - зімнеться, і додадуться додаткові параметри такі як, корекція часу, або наступний номер пароля, кількість невдалих спроб, останній пароль, час використання паролю та IP адреса кліента що його використовував.  
MOTP  test-m  1111 074581dae926835a -23 0  2d843b  2012-12-19T20:01:12L 172.16.1.133  
Додатково для захисту є директива "[OTPAuthLogoutOnIPChange](http://code.google.com/p/mod-authn-otp/wiki/Configuration)" , що зробить неможливість використання одноразового паролю якщо IP адреса клієнта була змінена протягом часу життя одноразового паролю, що визначається директивою "OTPAuthMaxLinger" і по замовчуванню становить 600 секунд.  
  
  
P.S. Для швидкого завантаження на мобільному, ось QR - код з прямим посиланням на завантаження - [Mobile-OTP - Java MIDlet](http://motp.sourceforge.net/#2):   
  

[![](http://qrfree.kaywa.com/?s=8&d=http%3A%2F%2Fmotp.sf.net%2FMobileOTP.jad)](http://qrfree.kaywa.com/?s=8&d=http%3A%2F%2Fmotp.sf.net%2FMobileOTP.jad)  
*http://motp.sf.net/MobileOTP.jad*
