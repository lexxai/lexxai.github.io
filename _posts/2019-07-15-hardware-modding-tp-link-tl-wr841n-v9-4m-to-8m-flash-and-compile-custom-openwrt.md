---
layout: post
title: "Hardware modding TP-Link TL-WR841N v9 4M to 8M Flash and compile custom OpenWrt"
date: 2019-07-15 23:15:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2019/07/hardware-modding-tp-link-tl-wr841n-v9.html
---

Нотатка для себе як як робив перетворення роутера [TP-Link TL-WR841N](https://openwrt.org/toh/tp-link/tl-wr841nd) v9, розширення пам'яті пам'яті 4M to 8M.  
  
Так як це не нова задача і в мережі є багато тлумачень як це зробити, зроблю для себе коротенькі нотатки.  
  
Я користуватися в основному цим відео -  [Відео: TP-Link WR841 16MB Flash Upgrade](https://youtu.be/l6rw1zo4A2c)  
  

#### І ось основні етапи:

1. Для збереження зони налаштувань wifi (ART), зробити dump рідної прошивки, або прошитися з tp-link на OpenWRT і зробити це з консолі.

   ```
   dd if=/dev/mtd4 of=/tmp/orig_art.bin
   ```
2. Демонтувати рідну flash пам'ять на 4Мб, прочитати програматором, вирізати останні 64K (ART) в окремий файл.
3. У новій flash пам'яті на 8Мб (у мене), прописати [U-Boot Mod](http://projects.dymacz.pl/u-boot_mod/u-boot_mod__tp-link_tl-wr841n_v9__20180223__git_master-7a540a78.bin).
4. Змонтувати flash пам'яті на 8Мб у роутері.
5. Підключити RS-232 інтерфейс.
6. Завантажитися.
7. Оновити ART.
8. Оновити прошивку на 8Мб (OpewWrt).

  

#### Створення OpewWrt прошивки  на 8Мб

  
Початкова інструкція з створення своєї прошивки OpenWRT:  
[OpenWrt Project: Beginners guide to building your own firmware](https://openwrt.org/docs/guide-user/additional-software/beginners-build-guide)  
  
Windows 10 users can install Ubuntu from Microsoft Store  

[![](/assets/images/blog/99bdd186e2657916-5f8922fd9106b2a0.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhrECINsGRkUJR3Q0EkRg15InmpuyjoKT4p6LI08C9Ep1XBLJRQlZ7rYKp8XXRoCTX1lZ4s4S0IvYt3Axje-TCwkcvRY-EHbmoMqGtjDdN5W1uKv_9oP0rllVEmvBxp8SNY41VgcVP_r1j2/s1600/841mod-03.PNG)  
*Windows 10 :Ubuntu 18.04 LTS*

```
sudo apt-get update
sudo apt-get upgrade
```

[![](/assets/images/blog/715b2acc99139b9d-07dfcec5f21c2047.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjKW5TfDTI8dJ4KFBXO-XsWyJxRpfi6W3iM8Ep8T5KEztPVWLZib91a3sCjeyxxrD2vHScRE94cHrcmPIB46v6plfuE7GUaKnpd3q0wjdeX5CUa2OpA3CW_GSQWYJXhlxbBNwLK8ZwykT-c/s1600/841mod-04.PNG)  
*Ubuntu : apt-get update*

  
[OpenWrt Project: Quick Image Building Guide](https://openwrt.org/docs/guide-developer/quickstart-build-images)  
  
First we need to make sure the dependencies are installed (for Debian/Ubuntu):
  
    

```
sudo apt install build-essential libncurses5-dev python unzip 
```

Get the OpenWrt source code:
  

```
git clone https://git.openwrt.org/openwrt/openwrt.git/

cd openwrt
git tag
git branch
git checkout v18.06.4
./scripts/feeds update -a
./scripts/feeds install -a
```

  
edit file openwrt/target~r71xx/image/tiny-tp-link.mk :  
from Windows environment:  
\\wsl$\Ubuntu-18.04\home\lexxai\openwrt\targets\ar71xx\image\  
or on Ubuntu by mc (sudo apt install mc)  
edit file /home/lexxai/openwrt/target~r71xx/image/tiny-tp-link.mk  
change  $(Device/tplink-4mlzma) to  $(Device/tplink-8mlzma)  
for your device in : define Device/tl-wr841-v9  

[![](/assets/images/blog/9d1b77d19bf8f29c-5a3ca58d03bf6b64.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjnKOD1-LPjG1r4OvA9JJN_Ymy1k3PnyVf-S3VrmsquQ8Ebc5dWoRZ2ehyphenhyphen-mYR9fnQu8AvxQ1hvR5i8x4qdyEGd_lcGHMA-gcvXiWza5aHoo9fjaQj5o-vWe-wcIluJR_WxaTfEVEgZVV5F/s1600/841mod-05.PNG)  
*Edit for 8M FLASH*

  

```
make menuconfig
```

  
If you want to build images for the “TL-WR841N v9” Wifi-Router, select:  
  
    “Target System” ⇒ “Atheros AR7xxx/AR9xxx”  
    “Subtarget” ⇒ “Devices with small flash”  
    “Target Profile” ⇒ “TP-LINK TL-WR841N/ND v9”  
  

[![](/assets/images/blog/2155bee3d1ece7b6-8a6f77f43b53a5b5.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEio0B9vgH-Cm61bU4CzENICmmcIBi-9xCxW7zgA6jYcerhQnXjoqz50ev_OgpSa_EKsxbYJ19tjV2FP_TVy4zXvsi-ehghRlpvjxaQuduPlA325yk3aVUWV1Q9M3Lml77wUUzNCAGuzzVBG/s1600/841mod-06.PNG)  
*make menuconfig*

  
Select Full hostapd  

[![](/assets/images/blog/439b68877580f91f-1a0c1d09faa074ce.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjArpCeeG4ra4Tu6xxRVG1r5tITqx8Tg-XhLCzSdjMEyem0JXAridZHb7kgtYxd084xxw64lgFaU9zvhgaCoqDWU73xSASUm8rHcHGnsM4Q6fMJQtYyORaPNEfGZYSuRPDAk7l3tmweuW-E/s1600/841mod-07.PNG)  
*Full hostapd*

 Select Full wpa-supplicant, wpad  

[![](/assets/images/blog/aced0b3f3cae1f35-7910b2be068bb1e8.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjpWJlZhabduUEKQ6qJddUStLcwu4Vz9Q5GA5D1UGQWbO6YdZ7JZ_dFyOPcCxeu8lcIc3IJe09xb2hs8UgggcerKQCRQ0e_0IF6UNCX49HJMwu3ilZf5tEL3pet_bFSSTJWmtNa6sD5EY7R/s1600/841mod-08.PNG)  
*Full wpa-supplicant, wpad*

  
Add web interface luci  

[![](/assets/images/blog/5d00efdfd6cfb2ef-1f4b3e19e92f0bd6.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiMsE5LxVqI-21GF8G2pa3MvhvTP37sf-kkrOmZ8VLt1-PvNMdE2BDT-SJRDp7k1tQZ_cfsJoJSL-rQeqcnFULVyfa2VdmvffVSByhaVNR-QaTgOYShha_PqI-2NglrU9rz3XJGjiazvrSI/s1600/841mod-09.PNG)

  
In the menu, select Exit and then Yes to save your settings. Now build the images. That may take some time:  
  

```
make -j9 V=s
```

  
9 is  8 CPU cores + 1  
  
Result image by path  
openwrt/bin/targets/ar71xx/tiny/openwrt-ar71xx-tiny-tl-wr841-v9-squashfs-factory.bin  
  
For found files in Windows  do trick: on ubuntu console  run: "explorer.exe ."  

[![](/assets/images/blog/e0f1865f932f9335-57ffa45924c7db14.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhq80fI2syQldNhwkXQO7iYHcr6IcS5d08Ma4VvBr9DRIQjLupVRpAVmXdIAPHeBNtBBJsbgPbgnh8klqWSkeC4uNJvqVzdB9PLYmqBDclghiZTRJ3_cKl1y0i5Fm3HZphFmLGasewmFuvi/s1600/841mod-10.PNG)  
*Windows 10: result flash image*

У мене програматор CH341A.  
Модифікований U-Boot завантажую в програматор і прошиваю чіп Winbond W25Q64FV.  
  

[![](/assets/images/blog/34af4e51c0197042-06933523e03a9abe.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh-dTm0M9wedm9jrLdbZOgspQX7mhoZymIZDwe9-iF5AG6p3sfY75Jkkr4WfD6CjSUmXhhtqqK3neRdsVO5OEK6T9zwcAqosRoef8fbcBHHmPKQ5CfUhu3x3syi7ZAcW3eCOIGvLJ431y8R/s1600/841mod-02.PNG)

  
Завантажуюсь з U-boot.  
Відновлюю ART через U-boot revovery http модуль.  
Готову прошивку openwrt-ar71xx-tiny-tl-wr841-v9-squashfs-factory.bin прошиваю через U-boot revovery http модуль.   
  
Консоль RS-242 в роутері на етапі завантаження.  
  

[![](/assets/images/blog/d6aa4bca82bbcdff-686c9889ae3443c5.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjPsSw7O1puQvhpBtgzR5TRn3KmKMDN351EMQXgQSRUjj5ffaFDkx7a1FtbYUX0dxBvzGerFx07A9G3KkwiMEafKT3hsXh2U4MUR8Bvr311stLOWVxEZP2d0LNJRpDANd8TZ7tDr5V6HwIP/s1600/841mod-01.PNG)
