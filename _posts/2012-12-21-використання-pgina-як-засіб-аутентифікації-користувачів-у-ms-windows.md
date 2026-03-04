---
layout: post
title: "Використання pGina як засіб аутентифікації користувачів у MS Windows"
date: 2012-12-21 11:58:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2012/12/pgina-ms-windows.html
---

[pGina](http://pgina.org/) це Open Source проект що дозволяє замінити традиційний модуль автентифікатор "MS Windows" (Windows Credential Provider/GINA) на програму pGina з додатковими можливостями за рахунок використання зовнішніх модулів - плагінів. [Як це працює](http://pgina.org/docs/v3.1/user.html) можна ознайомитися на сторінці проекту.  
  
У поточній [версії 3.0 присутні наступні додаткові модулі](http://pgina.org/docs/v3.0/index.html):  

* [LocalMachine](http://pgina.org/docs/v3.0/local_machine.html) - Плагін "local machine" управляє [автентифікацією](http://uk.wikipedia.org/wiki/%D0%90%D0%B2%D1%82%D0%B5%D0%BD%D1%82%D0%B8%D1%84%D1%96%D0%BA%D0%B0%D1%86%D1%96%D1%8F) і [авторизацією](http://uk.wikipedia.org/wiki/%D0%90%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D1%96%D1%8F) за обліковими рахунками користувачів , які існують на локальному комп'ютері.
* [LDAP Authentication](http://pgina.org/docs/v3.0/ldap.html) - Плагін  "LDAP Authentication" надає послуги [автентифікації](http://uk.wikipedia.org/wiki/%D0%90%D0%B2%D1%82%D0%B5%D0%BD%D1%82%D0%B8%D1%84%D1%96%D0%BA%D0%B0%D1%86%D1%96%D1%8F) через LDAP сервер.
* [MySQL Logger](http://pgina.org/docs/v3.0/mysql_logger.html) - Плагін  "MySQL Logger"  протоколює різноманітні події в базі даних [MySQL](http://uk.wikipedia.org/wiki/MySQL).
* [MySQL Authentication](http://pgina.org/docs/v3.0/mysql_auth.html) - Плагін  "MySQL Authentication" проводить [а](http://uk.wikipedia.org/wiki/%D0%90%D0%B2%D1%82%D0%B5%D0%BD%D1%82%D0%B8%D1%84%D1%96%D0%BA%D0%B0%D1%86%D1%96%D1%8F)[втентифікацію](http://uk.wikipedia.org/wiki/%D0%90%D0%B2%D1%82%D0%B5%D0%BD%D1%82%D0%B8%D1%84%D1%96%D0%BA%D0%B0%D1%86%D1%96%D1%8F)  користувачів облікові рахунки котрих збереженні у базі даних [MySQL](http://uk.wikipedia.org/wiki/MySQL).
* [Single User](http://pgina.org/docs/v3.0/single_user.html) - Плагін  "Single User" змушує усі [автентифікації](http://uk.wikipedia.org/wiki/%D0%90%D0%B2%D1%82%D0%B5%D0%BD%D1%82%D0%B8%D1%84%D1%96%D0%BA%D0%B0%D1%86%D1%96%D1%8F) та [авторизації](http://uk.wikipedia.org/wiki/%D0%90%D0%B2%D1%82%D0%BE%D1%80%D0%B8%D0%B7%D0%B0%D1%86%D1%96%D1%8F) користувачів для входу до комп'ютеру робити  під одним локальним обліковим записом.
* [Session Limit](http://pgina.org/docs/v3.0/session_limit.html) - Плагін  "Session Limit"  буде автоматично відключати сеанси користувачів після закінчення певного періоду часу.
* [Email Authentication](http://pgina.org/docs/v3.0/email_auth.html) - Плагін  "Email Authentication" під'єднується до вказаних поштових серверів за протоколами POP3 або IMAP та робить спробу [автентифікації](http://uk.wikipedia.org/wiki/%D0%90%D0%B2%D1%82%D0%B5%D0%BD%D1%82%D0%B8%D1%84%D1%96%D0%BA%D0%B0%D1%86%D1%96%D1%8F) на цьому сервері.
* [Modify Username](http://pgina.org/docs/v3.0/username_mod.html) - Плагін " Modify Username" зазвичай не буде використовуватися по собі. Він призначений для зміни введеного імені користувача, щоб вони відповідали певним стандартам первинного плагіну.

У [версії 3.1 тестується плагін](http://pgina.org/docs/v3.1/radius.html) для роботи з сервером [RADIUS](http://uk.wikipedia.org/wiki/RADIUS).  
У [попередніх версіях](http://pgina.sourceforge.net/index.php/PGina_2.x_Documentation) була можливість об'єднувати плагіни послідовно.   

Я проводив тестування,  з плагіном  "Email Authentication" запитань не виникло, з плагіном  "LDAP Authentication" треба було трохи розібратися. Може кому пригодяться мої етапи.  
  
Завдання використати "[pGina](http://pgina.org/)" та плагін "LDAP Authentication" при підключенні до розгорнутої "[Acive Directory](http://uk.wikipedia.org/wiki/Active_Directory)" Microsoft Windows домену "ec.khai.edu".  
Acive Directory це [LDAP](http://uk.wikipedia.org/wiki/LDAP "LDAP")-сумісна реалізація [інтелектуальної служби каталогів](http://uk.wikipedia.org/wiki/%D0%A1%D0%BB%D1%83%D0%B6%D0%B1%D0%B0_%D0%BA%D0%B0%D1%82%D0%B0%D0%BB%D0%BE%D0%B3%D1%96%D0%B2 "Служба каталогів") корпорації [Microsoft](http://uk.wikipedia.org/wiki/Microsoft "Microsoft") для [операційних систем](http://uk.wikipedia.org/wiki/%D0%9E%D0%BF%D0%B5%D1%80%D0%B0%D1%86%D1%96%D0%B9%D0%BD%D0%B0_%D1%81%D0%B8%D1%81%D1%82%D0%B5%D0%BC%D0%B0 "Операційна система") родини [Windows NT](http://uk.wikipedia.org/wiki/Windows_NT "Windows NT").  
Для отримання відповідей на запити [LDAP](http://uk.wikipedia.org/wiki/LDAP) "Acive Directory" потрібно зробити автентифіковане підключення до [LDAP](http://uk.wikipedia.org/wiki/LDAP) сервера використовуючи певний обліковий запис. Засобами керування обліковий записів я створив обліковий запис "ladpconnectonly" у домені "ec.khai.edu", глобальну групу "BIND". Обліковому запису "ladpconnectonly" назанчив групу BIND як головну, і виключив його з групи "Domain Users".  
  

[![](/assets/images/blog/f83dfb6809955909-f5c1eaf7d32ba9e5.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjPfF5vdMaA8oGymHsHNrTU8zO11hScQ_uVzC5SaaOnx8tH8VcOhxTpa433pOs2_w0TYpsTX5KYNh17RNBUx4K5QjcVkXt0u5cmbeImp2BsSS3zNvvm_pNGJNqgpISadIDOIfceG6T8gvbF/s1600/pGina-ldap-01.png)[![](/assets/images/blog/a6216b375313bf5f-4191fef287b8fc0b.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEidzExpT1Zdbfeb0Bk7XeA11_ZJL25CMUL1x8SKOKy_Wi6bwDgvfYenWr6NX0N6eKtVBeXLnBqtGpHvdT2v1LDys8zO3x4H8zrV893tCjNZIMtyXaGXWECDTY0Az_g3YLKngZHSEkxg4EMr/s1600/pGina-ldap-02.png)

  
Надалі, я почав спроби підключитися до "Acive Directory" з консолі під керуванням операціної системи FreeBSD використовуючи "[ldapsearch](http://www.openldap.org/software/man.cgi?query=ldapsearch&format=html)".  
> ldapsearch -h ec.khai.edu -p 389 -W -D "ladpconnectonly@ec.khai.edu" -x -b "dc=ec,dc=khai,dc=edu" samaccountname=petrov

Набираємо пароль на запит з консолі "Enter LDAP Password:" для користувача "ladpconnectonly".  
Отримаємо відповідь про користувача "Acive Directory" "petrov", домену "ec.khai.edu" на кшталт цієї:  
> # extended LDIF  
> #  
> # LDAPv3  
> # base <DC=ec,DC=khai,DC=edu> with scope subtree  
> # filter: samaccountname=petrov  
> # requesting: ALL  
> #  
> objectClass: top  
> objectClass: person  
> objectClass: organizationalPerson  
> objectClass: user

  
Тепер спробую налаштувати плагін "LDAP Authentication" у pGina.  
  

[![](/assets/images/blog/1fee0bd477502b2e-a3d6c0dc692a976c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiPh7ToUEFgPl4Ari-jGdQQBhqIAPTVxjANhy54vZ31SeCq37xP1kUpYjYpGNe9Q3SrGJzOhS1h9Ve9wQsgA5NX3iZrSLGkmgZBqW7qJjumYfcfkMZZYUMX9CinDiL5Ay9UQbLw0AD6kPNo/s1600/pGina-ldap-03.png)[![](/assets/images/blog/a134d81ff275324c-054126d53dc1f0e6.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhjthXbTrQkMJB8Hm29BHS-_su8nSqWMdkgwe8uT0NgRZmd0tRb9gUMoTUQ-unsr3stmB6VqNWSlv0DdfY7o6VMpGT6tWTsj8DdqZnLrNXGlXQHOKQzJQ51BTq8OwUzDOE4cx_xwBsMcRM4/s1600/pGina-ldap-04.png)

  

[![](/assets/images/blog/07a216b61b263443-4491a398c6670639.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi6ZQtnn-LAhDCYgag9-f4zbXYSxdGABlGVqTuwD-91Ba3mE0m4E5JNf9ivNROOto3jvRHThQ0K41Wye-JCRJgWLyMWyc3hkTXayWYosJ9-6AVWYok2Cuf3mHs70J3l_DGzNgvMte26K5yO/s1600/pGina-ldap-05.png)[![](/assets/images/blog/2281f2e56622e57a-b5beb33de9d799ef.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjue_P-l64ESnhPpEl81mvPGJbybPUHe42srw_dV5zRkrcW_Ji0S6CmVlvOPAvvKWoe8jLasH2WZmVPSiAY4-e6mahyphenhyphenw3C1thh5yCqRdQ1txlNaN_m9qDSDJGgL_bFgZAhuB_LlhMuXdCID/s1600/pGina-ldap-06.png)

  
 Як видно з зображень, у розділі "Plugin Selection" вибираємо плагін "LDAP Authentication" у якості дозволенного модуля аутентифікації та заходимо до конфігурації плагіну.  
LDAP Host прописуємо адресу нашого сервера де знаходитися  контролер домен з "Acive Directory". Відмічаємо "Search for DN", у полі "Search filter" прописуємо параметри "filter" для пошуку когерентного атрибута, у нашому випадку це "samaccountname=%u", де %u - буде замінено програмою "pGina" на введене ім'я користувача при авторизації, у моєму випадку це "petrov".  
У полі "Search Context(s)" прописуємо параметри контексту пошуку у LDAP сервері, у моєму випадку це ім'я домену "ec.khai.edu" записане у такій формі: "dc=ec,dc=khai,dc=edu". За бажанням можна зменшити коло пошуку у LDAP сервері задавши ім'я організаційної одинці де збережено користувача, наприклад "ou=students,dc=ec,dc=khai,dc=edu".  
У полях "Search  DN", "Search  Password" прописуємо ім'я користувача та його пароль під котрим ми робимо запити до LDAP сервера.  
У розділі "Simulation", підрозділ "Simulated Logon UI" у полі Username/Password вводимо  ім'я та пароль користувача з "Acive Directory", та натискаємо на клавішу з зображенням зеленого трикутника. В полі "Live Log", можемо бачити журнал роботи сеансу підключення та помилки.  
Підрозділ "Results" показує результати аутентифікації, авторизації, програм шлюзу.  
  
Дуже цікаві статті на цю тему:  
[Використання pGina для автентификації в системах MS Windows (частина 1).](http://ebushkov.blogspot.com/2011/06/pgina-ms-windows-1.html)   
[Використання pGina для автентификації в системах MS Windows (частина 2).](http://ebushkov.blogspot.com/2011/06/pgina-ms-windows-2.html)  
  
[Викорситання pGina як двофакторну автентифікацію з YubiKey OTP.](http://static.yubico.com/var/uploads/pdfs/YubiKey%20authentication%20for%20Windows%20login%20using%20pGina%20and%20RADIUS_2011-03-16.pdf)
