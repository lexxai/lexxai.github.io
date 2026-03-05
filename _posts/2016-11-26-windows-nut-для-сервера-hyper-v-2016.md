---
layout: post
title: "Windows NUT для сервера Hyper-V 2016"
date: 2016-11-26 01:53:00 +0000
tags: ["administration", "Hyper-V", "NUT", "Virtualization", "windows"]
blogger_orig_link: https://lexxai.blogspot.com/2016/11/windows-nut-hyper-v-2016.html
---

Спроба налаштувати NUT для Hyper-V 2016.  
Маю спільний UPS з двома серверами.  
1. [FreeNAS](https://uk.wikipedia.org/wiki/FreeNAS) з [NUT](http://networkupstools.org/) під'єднаний до UPS  
2. Hyper-V 2016  
  
Завдання правильно вимикати обидва сервери.  
На сервері з [FreeNAS](https://uk.wikipedia.org/wiki/FreeNAS), налаштовано [NUT](http://networkupstools.org/) через графічний інтерфейс  

[![](/assets/images/blog/a550e120c5fcf598-08207216a8627f01.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhIr3qanB5BFOYhSeHI-tdqE-JktD-Dv63C5OkipHo7aJE64Iue0eAiqNbYKCrQWxvarfAkpfVPiL4dyBo10J9MW7YGVWOrgevJaligBxux1LdTmgMmKfk9QF_2FEWJrAIewkb7zhsCXTPM/s1600/freenas-nut.png)  
*Налаштування NUT у FreeNAS*

1. Завантажити http://networkupstools.org/download.html#\_binary\_packages Binary package .msi for Windows
2. Завантажити бібліотеки OpenSSL:  https://indy.fulgan.com/SSL/openssl-x.x.x-i386-win32.zip
3. Передати це до сервера HV, я роблю через FAR і локальну мережу, або Mozila Firefox браузер.
4. Виконати інсталяцю msi : NUT-Installer-x.x.x.msi
5. Розпакуіати openssl-x.x.x-i386-win32.zip.  Я роблю через FAR або 7-zip.
6. Скопіювати бібліотеки libeay32.dll, ssleay.dll до NUT теки sbin
7. Скопіювати libgcc\_s\_dw2-1.dll з NUT теки bin до  теки sbin
8. Налаштувати конфігураційний файл: etc/nut.conf: MODE=netclient
9. Налаштувати конфігураційний файл: etc/upsmon.conf: MONITOR ups@server2 1 upsmon\_remote pass slave
10. Перезапустити службу "Network UPS Tools": net stop "Network UPS Tools", net start "Network UPS Tools". Або з диспетчера завдань (taskmgr.exe).
11. Перевірити з'єднання з майстром : netstat -n | find ":3493"

  

[![](/assets/images/blog/ac50ba8a41864931-ef7f95daa18c3044.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg6CDEapLyT9XubQbqt6F5JG8x6K0hJaVrMKVYYnsqHPrkvClDNhuuwQJKKbgY8CKr_OBsEzQRy6KgKV7GmySHad89esC_laQ9exyDw90s5oFXjxx2_j6Vo6KJFk9dmMplIjFAoAz0YVsgo/s1600/hv-nut.png)  
*Hyper-V, 2016, Windows NUT*

  
Далі тестування, чи працює...  
Працює.
