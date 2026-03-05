---
layout: post
title: "DANtes OpenWRT kernel 2.6.32.28 (TP-LINK TL-WR1043ND)"
date: 2011-10-06 10:55:00 +0000
tags: ["Atheros", "firmware", "kernel", "linux", "openwrt", "RS232", "TP-Link 1043", "Wi-Fi", "Wireless"]
blogger_orig_link: https://lexxai.blogspot.com/2011/10/dantes-openwrt-kernel-263228-tp-link-tl.html
---

Є такий аматор фахівець з модифікацій [прошивок для роутерів як DANtes](http://sat-forum.su/index.php/topic,8863.0.html). Велике дякую йому.  
Так одна з останніх прошивок для роутера TP-LINK TL-WR1043ND побудована на основі таких даних:  
> Router Model TP-LINK TL-WR1043ND  
> Firmware Version OpenWrt Firmware Kamikaze (r25068) /   LuCI Trunk 0.10+svn6942  
> Kernel 2.6.32.28

Особливість цієї прошивки - наявність пакету MPCS з підтримкою USB-COM адаптерів для підключення супутникових ресиверів застарілого типу, що мають тільки RS232 порт для комунікації з зовнішніми пристроями, наприклад OpenBox 800.  
Прошивка була встановлена зі стандартної, все працює. Але щоб розширити функціонал є можливість додавання додаткових пакунків. Але деякі пакунки зав'язані на версію ядра операційної системи і знайти їх досить проблематично готовими, так як існують пакунки для більш нових версій.  
  
Так необхідно було включити додатково у роутері підтримку зовнішніх накопичувачів USB-HDD.  
> kmod-fs-ntfs   
> kmod-usb-storage

Якзо завантижити ці пакунки зі сховища пакунків opewrt, то  отримаємо подібні помилки:   
> Downloading [http://downloads.openwrt.org/snapshots/trunk/ar71xx/packages/](http://downloads.openwrt.org/snapshots/trunk/ar71xx/packages/mountd_0.1-4_ar71xx.ipk)....  
> Multiple packages (kmod-usb-core and kmod-usb-core) providing same name marked HOLD or PREFER. Using latest.Collected errors:  
>  \* satisfy\_dependencies\_for: Cannot satisfy the following dependencies for mountd:  
>  \*      kernel (= 2.6.39.4-1) \*         kernel (= 2.6.39.4-1) \*         kernel (= 2.6.39.4-1) \*  
>  \* opkg\_install\_cmd: Cannot install package ....

Тому потрібно зробити самому пакунок на основі поточного ядра Kernel 2.6.32.28.  
  

1. Потрібна віртуальна машина, наприклад [VMWare Player](http://www.vmware.com/) (безкоштовний)
2. Образ Linix системи, для швидкості я звантажив вже готовий [образ](http://torrents.thoughtpolice.co.uk/ubuntu-server-11.04-i386.zip.torrent) встановленої системи Ubuntu Server 11.04 для VMWare (http://thoughtpolice.co.uk).
3. Налаштовуємо мережний адаптер віртуального образу як Bridged.
4. Підключаємося до віртуальної машини, завантажується входимо до системи використовуючи логін notroot, пароль вказаний у файлі README-vmware-image.txt.
5. Ubuntu Server використвує DHCP для отримання IP у роутера системи. У привітанні системи ви побачите цю адресу.
6. Змінюємо пароль на свій,  *passwd*
7. Оновлюємо базу пакунків, *sudo apt-get update*
8. Встановлюємо Midnight Commander mc,  *sudo apt-get install mc*
9. Тепер жити простіше, *mc*
10. Створюємо теку openwrt і заходимо до неї *cd openwrt*
11. Завантажуємо образ Kernel 2.6.32.28, *wget http://ftp.gts.lug.ro/kernel/linux/kernel/v2.6/longterm/v2.6.32/linux-2.6.32.28.tar.bz2*
12. Завантажуємо trunk Openwrt release 25068 він на базі linux-2.6.32.27, до теки "trunk-25068", *svn co svn://svn.openwrt.org/openwrt/trunk@25068 trunk-25068*
13. Редагуємо файл ~/openwrt/trunk-25068/target/linux/ar71xx/Makefile, змінюючи     *LINUX\_VERSION:=2.6.32.27* на  *LINUX\_VERSION:=2.6.32.28*
14. Переходимо до  теки *~/openwrt/trunk-25068*
15. Запускаємо конфігуратор *make menuconfig*
16. Вибираємо *Target System (Atheros AR71xx/AR7240/AR913x), Target Profile (TP-LINK TL-WR1043ND v1)*
17. Вибираємо для USB-COM: Kernel modules, *USB Support, kmod-usb-serial, kmod-usb-serial-pl2303, kmod-usb-serial-ftdi*
18. Вибираємо для USB-HDD: *Kernel modules, kmod-usb-storage,  kmod-fs-msdos, kmod-fs-ntfs, kmod-fuse, kmod-scsi-core*
19. Вибираємо для Wifi Entreprіse Security (якщо потрібно):  *Network, hostapd*
20. Зберігаємо налаштування: Esc,  *Do you wish to save your new OpenWrt configuration? , Yes*
21. Компіляція: *make* або більш детальне *make V=99*
22. У процесі компіляції потрібен доступ до мережі інтернет для завантаження компонентів, вони завантажуються до теки *~/openwrt/trunk-25068/dl*
23. Якщо будуть проблеми з завантаженням  *linux-2.6.32.28.tar.bz2*, то можна перенести попередньо завантажений файл  до теки *~/openwrt/trunk-25068/dl*
24. Результат буде в теці *~/openwrt/trunk-25068/bin/ar71xx/packages*, файли з розширенням .ipk
25. Для доступу з роутера до цих файлів встановлюємо http server, наприклад apache: *sudo apt-get  install apache2*
26. Підключаємо теку з готовими пакетами до http server,  *ln -s ~/openwrt/r25068/trunk/trunk-25068/bin/ar71xx /var/www/openwrt*
27. Доступ до файлів буде як адреса *http://ip-ubuntu-server/openwrt/packages*
28. Далі підключаємося до роутера через SSH я використовую PuTTY.
29. Завантажуэмо наші пакети готові для ядра 2.6.32.28: *opkg install http://ip-ubuntu-server/openwrt/packages/kmod-usb-storage\_2.6.32.28-1\_ar71xx.ipk*
30. Повторюємо для усіх необхідних надалі пакунків, *kmod-scsi-core\_2.6.32.28-1\_ar71xx.ipk, kmod-fs-ntfs\_2.6.32.28-1\_ar71xx.ipk ....*
31. Надалі вже налаштовуємо підключений HDD

  

P.S.

Завантаження модуля - *insmod*, вивантаження *rmmod*, перелік завантажених *lsmod*

  

[![](/assets/images/blog/bd2f22713a9e03b2-a689555767e4bbf2.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiyUrCBqu6XZYg0nwTwB5gCJKqFnuO-iBbmZebAqET8qDb4ALOlwSRQozsex35rFd5t3atCeJK9ZqlKMNT8z1Hg9Udhcl_KkEXseRZUEnPrpmOa9UW_FvfkWscF511wyttvZuJfQ_1q1MsK/s1600/net-bridged.PNG)  
*VMware - network Bridged*

  

[![](/assets/images/blog/904d9844ceb2742e-9753806c858e0a3e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiGS7m99E1ALjz1VqdNeBvZzbOANsp0ppuxTVc55XD_Cjj4KulZC_yTglun1jRxxhBV8FFdGdPCmsnjJ6gIjFsVs24vYhROoRMxT76yg3ZW85fFDHQTwx-K9H3G4ohErU6yrHNuLHtyYY7o/s1600/manuconfig.PNG)  
*Ubuntu Server: make menuconfig*

[![](/assets/images/blog/dd0ac7ebda4bf91c-732d746d7a327822.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg2461rUdfrgijpVuBljQddAQv_KABPXf2IYXMXomkUc3ud68qcOkWf92EdH55sZtBdfpSAz4tNHbuD1Mnz3iHQhmRAl-6ZivvDUy_QCIW3N5sIQHlPvz7eoUCdB1tLIZD7-sZ5WP353c4Y/s1600/opkg-1.PNG)  
*Роутер: opkg list-installed*

[![](/assets/images/blog/6054be412e1bc457-1c86cb2c44aca0d8.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEho8C88W8C89X8ETzChJ8ukHdq_Q4yLv00AerJlrf9CQYeRWAUautNAFxYqsZgUJZt-zql38cCHYL1v48BDah7r-SnIdWSOrNWLKZIOuFFWabgcSFu9bfDCuT-5QIDvsI105fNY9iXz1acF/s1600/logread.PNG)  
*Роутер: підключення HDD*
