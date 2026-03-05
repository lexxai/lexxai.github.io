---
layout: post
title: "TP-Link WR1043N OpenWrt Attitude Adjustment"
date: 2012-08-19 23:00:00 +0000
tags: ["3G", "ar71xx", "DLNA", "ext4", "ip_conntrack", "iptv", "ipv6", "open-wrt", "openwrt", "TP-Link 1043", "usb audio", "usb ethernet", "usb storage", "VLAN", "web cam", "Wi-Fi", "Wireless", "xupnpd"]
blogger_orig_link: https://lexxai.blogspot.com/2012/08/tp-link-wr1043n-openwrt-attitude.html
---

У зв'язку з використанням IPv6, оновився до останньої версії OpenWrt.  
  
Router Name OpenWrt  
Router Model TP-Link TL-WR1043N/ND v1  
  
Firmware Version  
OpenWrt Attitude Adjustment r33206 / LuCI Trunk (trunk+svn9073)  
Kernel Version 3.3.8  
  
Оновився до r33276,LuCI Trunk (trunk+svn9138)  Aug 27 19:07:23 MSK 2012 mips GNU/Linux  
  
           
  

### IPv6

  
IPv6 - парцює  
 */etc/config/network*:   
config interface 'wan'  
        option ifname 'eth0.2'  
        option proto 'static'  
        option ipaddr 'ipv4IP'  
        option netmask 'ipv4IP\_mask'  
        option gateway 'ipv4ISP\_gw'  
        option dns 'ipv4ISP\_dns ipv6ISP\_dns 2001:4860:4860::8888'  
        option ip6addr 'ipv6IP/ipv6IP\_prefix'  
        option ip6gw   'ipv6ISP\_gw'  
  
ping -6 google.com  
PING google.com (2a00:1450:400f:800::100e): 56 data bytes  
64 bytes from 2a00:1450:400f:800::100e: seq=0 ttl=57 time=37.560 ms  
64 bytes from 2a00:1450:400f:800::100e: seq=1 ttl=57 time=37.437 ms  
64 bytes from 2a00:1450:400f:800::100e: seq=2 ttl=57 time=37.203 ms  
  

### mpcs

mpcs - працює  
  

### xupnpd - трансфер IPTV до DLNA

  
xupnpd - парцює */etc/init.d.xupnpd*:  
#!/bin/sh /etc/rc.common  
# Sample init script reading its  
# configuration from /etc/config/package  
START=99  
STOP=99  
  
start() {  
    XUPNPDROOTDIR=/etc/xupnpd-rc /etc/xupnpd-rc/xupnpd-ar71xx  
}  
  
stop () {  
    killall xupnpd-ar71xx  
}  
  
  
  

### USB Devices

  
  

#cat /proc/bus/usb/devices

T:  Bus=01 Lev=00 Prnt=00 Port=00 Cnt=00 Dev#=  1 Spd=480  MxCh= 1  
B:  Alloc=  3/800 us ( 0%), #Int=  4, #Iso=  0  
D:  Ver= 2.00 Cls=09(hub  ) Sub=00 Prot=01 MxPS=64 #Cfgs=  1  
P:  Vendor=1d6b ProdID=0002 Rev= 3.03  
S:  Manufacturer=Linux 3.3.8 ehci\_hcd  
S:  Product=Generic Platform EHCI Controller  
S:  SerialNumber=ehci-platform  
C:\* #Ifs= 1 Cfg#= 1 Atr=e0 MxPwr=  0mA  
I:\* If#= 0 Alt= 0 #EPs= 1 Cls=09(hub  ) Sub=00 Prot=00 Driver=hub  
E:  Ad=81(I) Atr=03(Int.) MxPS=   4 Ivl=256ms

.....  
  
T:  Bus=01 Lev=02 Prnt=02 Port=03 Cnt=03 Dev#= 24 Spd=480  MxCh= 0  
D:  Ver= 2.00 Cls=00(>ifc ) Sub=00 Prot=00 MxPS=64 #Cfgs=  1  
P:  Vendor=152d ProdID=2329 Rev= 1.00  
S:  Manufacturer=JMicron  
S:  Product=USB to ATA/ATAPI Bridge  
S:  SerialNumber=506FFFFFFFFF  
C:\* #Ifs= 1 Cfg#= 1 Atr=c0 MxPwr=  2mA  
I:\* If#= 0 Alt= 0 #EPs= 2 Cls=08(stor.) Sub=06 Prot=50 Driver=usb-storage  
E:  Ad=81(I) Atr=02(Bulk) MxPS= 512 Ivl=0ms  
E:  Ad=02(O) Atr=02(Bulk) MxPS= 512 Ivl=0ms  
  
  
Цікаве, про режим підключення пристрою: USB Ver=2.00 Spd=480  
  

#opkg install usbutils

#lsusb

Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub

...  
Bus 001 Device 024: ID 152d:2329 JMicron Technology Corp. / JMicron USA Technology Corp. JM20329 SATA Bridge

#lsusb -t  
Bus#  1  
`-Dev#   1 Vendor 0x1d6b Product 0x0002  
  `-Dev#   2 Vendor 0x0409 Product 0x0059  
    |-Dev#   3 Vendor 0x0409 Product 0x0059  
    | |-Dev#   6 Vendor 0x0b95 Product 0x7720  
    | |-Dev#   7 Vendor 0x067b Product 0x2305  
    | |-Dev#   8 Vendor 0x067b Product 0x2303  
    | `-Dev#   9 Vendor 0x0d8c Product 0x0006  
    |-Dev#   4 Vendor 0x054c Product 0x02a5  
    `-Dev#  24 Vendor 0x152d Product 0x2329  
  
  
  

### WebCAM

  
[webcam](http://wiki.openwrt.org/doc/howto/webcam) - працює з камерою Logitech C270, 800x600, 10 fps (r148-4)  
*/etc/config/mjpg-streamer*:   
config mjpg-streamer core  
        option enabled          "1"  
        option device           "/dev/video0"  
        option resolution       "800x600"  
        option fps              "10"  
        option www              "/www/webcam"  
        option port             "8080"  
  
[ <http://wiki.openwrt.org/doc/howto/webcam> ]  
  
  
  

### USB Storage

usb storage -працює, створив файлову систему на флешці ext4: fdisk, e2fsprogs**`:`** /usr/sbin/mkfs.ext4 /dev/sda1  

mkdir -p /mnt/usb  
touch /mnt/usb /USB\_DISK\_NOT\_PRESENT  
chmod -R 444 /mnt/usb

mount -t ext4 /dev/sda1 /mnt/usb  
  
Для автомонтування opkg install block-mount, */etc/config/fstab*:  
config mount  
        option target   /mnt/usb  
        option device   /dev/sda1  
        option fstype   ext4  
        option options  rw,sync  
        option enabled  1  
        option enabled\_fsck 0   
  
[ <http://wiki.openwrt.org/doc/howto/usb.storage> ]  

### 

### Встановлення пакетів на флешку

[Встановлення пакетів на флешку](http://wiki.openwrt.org/doc/techref/opkg#mount.point)   
ln -s /mnt/usb /opt  
  
*/etc/opkg.conf*:  
src/gz attitude\_adjustment http://downloads.openwrt.org/snapshots/trunk/ar71xx/packages  
dest root /  
dest ram /tmp  
dest usb /opt  
lists\_dir ext /var/opkg-lists  
option overlay\_root /overlay  
  

```
opkg -dest usb packagename ... 
```

```
 
```

Але... файли що повинні були встановленні до /etc/... будуть встановлені до /opt/etc/...  
і тому щоб система знала про їх існування потрібно буде встановлювати символічні зв'язки на ці файли,  на кшталт ln -s /opt/etc/init.d/\* /etc.init.d/,  
а також видаляти їх перед видаленням пакунку ln -f /opt/etc/init.d/\* /etc/init.d  
Тому для деяких пакунків, наприклад "luci\_\*", та "kmod-\*" я рекомендував би встановлювати до root.opkg  
Для створення зв'язків make-link.sh:  
#!/bin/sh  
source\_dir="/opt/etc"  
target\_dir="/etc"  
cd ${source\_dir}  
pwd  
for a in \* ; do  
if [ -d "${target\_dir}/${a}" ]  
then  
    echo ln -s "${source\_dir}/$a/\* ${target\_dir}/$a/\*" ;  
    ln -s ${source\_dir}/$a/\* ${target\_dir}/$a ;  
else  
    echo ln -s "${source\_dir}/$a ${target\_dir}" ;  
    ln -s ${source\_dir}/$a ${target\_dir}  
fi  
done  
Для видалення зв'язків flush-link.sh:  
#!/bin/sh  
source\_dir="/opt/etc"  
target\_dir="/etc"  
cd ${source\_dir}  
pwd  
for a in \* ; do  
if [ -d "${target\_dir}/${a}" ]  
then  
    echo ln -s "${source\_dir}/$a/\* ${target\_dir}/$a/\*" ;  
    ln -f ${source\_dir}/$a/\* ${target\_dir}/$a ;  
else  
    echo ln -s "${source\_dir}/$a ${target\_dir}" ;  
    ln -f ${source\_dir}/$a ${target\_dir}  
fi  
done  
  
подібне зробив для шляхів тек що пов'язанні з LuCI  
source\_dir="/opt/usr/lib/lua/luci"  
target\_dir="/usr/lib/lua/luci"  
source\_dir="/opt/usr/lib/lua/luci/model"  
target\_dir="/usr/lib/lua/luci/model"  
Якщо у */etc/opkg.conf* першим розмістити dest usb /opt, то за примовчуванням буде встановлено пакунки на флешку.   
  

```
 
```

```
Example:
```

```
opkg -d usb install bind-host
Installing bind-host (9.9.1-P1-1) to usb...
Downloading http://..../ar71xx/packages/bind-host_9.9.1-P1-1_ar71xx.ipk.
Installing bind-libs (9.9.1-P1-1) to usb...
Downloading http://..../ar71xx/packages/bind-libs_9.9.1-P1-1_ar71xx.ipk.
Installing libopenssl (1.0.1c-1) to usb...
Downloading http://..../ar71xx/packages/libopenssl_1.0.1c-1_ar71xx.ipk.
Configuring libopenssl.
Configuring bind-libs.
Configuring bind-host.
```

```
opkg  list-installed  |grep bind-host
bind-host - 9.9.1-P1-1
```

```
 
```

### USB Storage as root

Щоб уникнути таких проблем, я монтую флешку як root, що уникає використання пам'яті роутера.  
  
Поки флешка змонтована як /mnt/usb, я копіюю до неї зміст теки /overlay.  
Тепер змінюємо   /etc/config/fstab:  
  

config mount

option target   /

option device   /dev/sda1

option fstype   ext4

option options  rw,sync

option enabled  1

option enabled\_fsck 0

option is\_rootfs 1

  
  
Та перезавантажуємо роутер, reboot.  
  
Після перезавантаження, якщо все гаразд то бачу що використовується флешка я основний носій:  
root@OpenWrt:~# df -h  
Filesystem                Size      Used Available Use% Mounted on  
rootfs                  964.9M     57.0M    859.5M   6% /  
/dev/root                 1.5M      1.5M         0 100% /rom  
tmpfs                    14.2M      2.3M     11.9M  16% /tmp  
tmpfs                   512.0K         0    512.0K   0% /dev  
/dev/sda1               964.9M     57.0M    859.5M   6% /overlay  
overlayfs:/overlay      964.9M     57.0M    859.5M   6% /  
  
[ <http://wiki.openwrt.org/doc/howto/extroot> ]   
  
  

### Форматування ext4 у роутері

#opkg install fdisk e2fsprogs  

root@OpenWrt:~#fdisk /dev/sdb

Command (m for help): m

Command action  
   a   toggle a bootable flag  
   b   edit bsd disklabel  
   c   toggle the dos compatibility flag  
   d   delete a partition  
   l   list known partition types  
   m   print this menu  
   n   add a new partition  
   o   create a new empty DOS partition table  
   p   print the partition table  
   q   quit without saving changes  
   s   create a new empty Sun disklabel  
   t   change a partition's system id  
   u   change display/entry units  
   v   verify the partition table  
   w   write table to disk and exit  
   x   extra functionality (experts only)

Command (m for help): p

Disk /dev/sdb: 320.1 GB, 320072933376 bytes  
255 heads, 63 sectors/track, 38913 cylinders, total 625142448 sectors  
Units = sectors of 1 \* 512 = 512 bytes  
Sector size (logical/physical): 512 bytes / 512 bytes  
I/O size (minimum/optimal): 512 bytes / 512 bytes  
Disk identifier: 0x3e8b1530  
  
   Device Boot      Start         End      Blocks   Id  System

Command (m for help): n  
Partition type:  
   p   primary (0 primary, 0 extended, 4 free)  
   e   extended  
Select (default p): p  
Partition number (1-4, default 1):  
Using default value 1  
First sector (2048-625142447, default 2048):  
Using default value 2048  
Last sector, +sectors or +size{K,M,G} (2048-625142447, default 625142447): +200M  
Partition 1 of type Linux and of size 200 MiB is set

Command (m for help): n  
Partition type:  
   p   primary (1 primary, 0 extended, 3 free)  
   e   extended  
Select (default p): p  
Partition number (1-4, default 2):  
Using default value 2  
First sector (411648-625142447, default 411648):  
Using default value 411648  
Last sector, +sectors or +size{K,M,G} (411648-625142447, default 625142447):  
Using default value 625142447  
Partition 2 of type Linux and of size 297.9 GiB is set  
  
Command (m for help): p  
  
Disk /dev/sdb: 320.1 GB, 320072933376 bytes  
255 heads, 63 sectors/track, 38913 cylinders, total 625142448 sectors  
Units = sectors of 1 \* 512 = 512 bytes  
Sector size (logical/physical): 512 bytes / 512 bytes  
I/O size (minimum/optimal): 512 bytes / 512 bytes  
Disk identifier: 0x3e8b1530  
  
   Device Boot      Start         End      Blocks   Id  System  
/dev/sdb1            2048      411647      204800   83  Linux  
/dev/sdb2          411648   625142447   312365400   83  Linux

Command (m for help): t  
Partition number (1-4): 1  
Hex code (type L to list codes): L  
  
 0  Empty           24  NEC DOS         81  Minix / old Lin bf  Solaris  
 1  FAT12           27  Hidden NTFS Win 82  Linux swap / So c1  DRDOS/sec (FAT-  
 2  XENIX root      39  Plan 9          83  Linux           c4  DRDOS/sec (FAT-  
 3  XENIX usr       3c  PartitionMagic  84  OS/2 hidden C:  c6  DRDOS/sec (FAT-  
 4  FAT16 <32M      40  Venix 80286     85  Linux extended  c7  Syrinx  
 5  Extended        41  PPC PReP Boot   86  NTFS volume set da  Non-FS data  
 6  FAT16           42  SFS             87  NTFS volume set db  CP/M / CTOS / .  
 7  HPFS/NTFS/exFAT 4d  QNX4.x          88  Linux plaintext de  Dell Utility  
 8  AIX             4e  QNX4.x 2nd part 8e  Linux LVM       df  BootIt  
 9  AIX bootable    4f  QNX4.x 3rd part 93  Amoeba          e1  DOS access  
 a  OS/2 Boot Manag 50  OnTrack DM      94  Amoeba BBT      e3  DOS R/O  
 b  W95 FAT32       51  OnTrack DM6 Aux 9f  BSD/OS          e4  SpeedStor  
 c  W95 FAT32 (LBA) 52  CP/M            a0  IBM Thinkpad hi eb  BeOS fs  
 e  W95 FAT16 (LBA) 53  OnTrack DM6 Aux a5  FreeBSD         ee  GPT  
 f  W95 Ext'd (LBA) 54  OnTrackDM6      a6  OpenBSD         ef  EFI (FAT-12/16/  
10  OPUS            55  EZ-Drive        a7  NeXTSTEP        f0  Linux/PA-RISC b  
11  Hidden FAT12    56  Golden Bow      a8  Darwin UFS      f1  SpeedStor  
12  Compaq diagnost 5c  Priam Edisk     a9  NetBSD          f4  SpeedStor  
14  Hidden FAT16 <3 61  SpeedStor       ab  Darwin boot     f2  DOS secondary  
16  Hidden FAT16    63  GNU HURD or Sys af  HFS / HFS+      fb  VMware VMFS  
17  Hidden HPFS/NTF 64  Novell Netware  b7  BSDI fs         fc  VMware VMKCORE  
18  AST SmartSleep  65  Novell Netware  b8  BSDI swap       fd  Linux raid auto  
1b  Hidden W95 FAT3 70  DiskSecure Mult bb  Boot Wizard hid fe  LANstep  
1c  Hidden W95 FAT3 75  PC/IX           be  Solaris boot    ff  BBT  
1e  Hidden W95 FAT1 80  Old Minix  
Hex code (type L to list codes): 82  
Changed system type of partition 1 to 82 (Linux swap / Solaris)

Command (m for help): p  
  
Disk /dev/sdb: 320.1 GB, 320072933376 bytes  
255 heads, 63 sectors/track, 38913 cylinders, total 625142448 sectors  
Units = sectors of 1 \* 512 = 512 bytes  
Sector size (logical/physical): 512 bytes / 512 bytes  
I/O size (minimum/optimal): 512 bytes / 512 bytes  
Disk identifier: 0x3e8b1530  
  
   Device Boot      Start         End      Blocks   Id  System  
/dev/sdb1            2048      411647      204800   82  Linux swap / Solaris  
/dev/sdb2          411648   625142447   312365400   83  Linux

Command (m for help): w  
The partition table has been altered!  
  
Calling ioctl() to re-read partition table.  
Syncing disks.  
  

root@OpenWrt:~# mkfs.ext4 /dev/sdb2

mke2fs 1.42.4 (12-June-2012)  
Filesystem label=  
OS type: Linux  
Block size=4096 (log=2)  
Fragment size=4096 (log=2)  
Stride=0 blocks, Stripe width=0 blocks  
19529728 inodes, 78091350 blocks  
3904567 blocks (5.00%) reserved for the super user  
First data block=0  
Maximum filesystem blocks=0  
2384 block groups  
32768 blocks per group, 32768 fragments per group  
8192 inodes per group  
Superblock backups stored on blocks:  
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,  
        4096000, 7962624, 11239424, 20480000, 23887872, 71663616  
  
Allocating group tables: done  
Writing inode tables: done  
Creating journal (32768 blocks): done  
Writing superblocks and filesystem accounting information: done

root@OpenWrt:~# mkswap /dev/sdb1  
Setting up swapspace version 1, size = 204796 KiB  
no label, UUID=70431cf4-9d83-41b1-b660-2a95be237762

root@OpenWrt:~# swapon UUID=70431cf4-9d83-41b1-b660-2a95be237762

root@OpenWrt:~# logread

aug 29 00:11:20 OpenWrt kern.info kernel: [77496.750000] Adding 204796k swap on /dev/sdb1.  Priority:-1 extents:1 across:204796k

  
root@OpenWrt:~# free  
             total         used         free       shared      buffers  
Mem:         29160        26756         2404            0          948  
-/+ buffers:              25808         3352  
Swap:       204796          348       204448  
  
  
root@OpenWrt:~# blkid  
/dev/mtdblock2: TYPE="squashfs"  
/dev/sda1: UUID="faef8540-92d4-426c-a6db-549596a2baac" TYPE="ext4"  
/dev/sdb1: UUID="70431cf4-9d83-41b1-b660-2a95be237762" TYPE="swap"  
/dev/sdb2: UUID="d3096f9a-55c1-4a54-a665-1f055089f037" TYPE="ext4"  
  

/etc/confif/fstab:

config mount  
        option uuid 'd3096f9a-55c1-4a54-a665-1f055089f037'  
        option target '/mnt/hddstore'  
        option fstype 'ext4'  
        option options 'rw,sync'  
        option enabled '1'  
  
config swap  
        option uuid '70431cf4-9d83-41b1-b660-2a95be237762'  
        option enabled '1'

config mount  
        option options 'rw,codepage=866,iocharset=cp1251,umask=000,dmask=000,fmask=000,uid=65534,gid=65534'  
        option fstype 'vfat'  
        option uuid 'E6D6-620A'  
        option target '/mnt/sambafat32'  
        option enabled '0'  
  
  
  

### Вимикаємо зовнішній накопичувач коли він простоює

 opkg install luci-app-hd-idle в залежності буде встановлено пакунок hd-idle.  
  
*/etc/config/hd-idle*:  
 config hd-idle  
        option idle\_time\_unit 'minutes'  
        option enabled '1'  
        option disk 'sdb'  
        option idle\_time\_interval '10'  
        option enable\_debug '0'  
  
За цими на лаштунками 10 хвилин простою і накопичувач (/dev/sdb) переходить до стану відпочинку.  
  
/etc/init.d/hd-idle enable start   
  
  
Налаштувати також можна, через інтерфейс LuCI/services/hd-idle.  
  

### 3G, CDMA, EV-DO

Працює, на базі мого терміналу CDMA - LG 639, від "People Net"  
  
Про  налаштування багато написанно, але головне що мій термінал відгугується на opkg install "kmod-usb-acm".  
root@OpenWrt:~# dmesg |grep acm  
cdc\_acm 1-1.4:1.0: ttyACM0: USB ACM device  
usbcore: registered new interface driver cdc\_acm  
cdc\_acm: USB Abstract Control Model driver for USB modems and ISDN adapters  
Для зручного налаштування додав до LuCI інтерфейсу,  opkg install luci-proto-3g  
  
[ <http://wiki.openwrt.org/doc/recipes/3gdongle> ]   
  

### USB AUDIO

Якось від ноутбука досталася мені док станція Notebook Dock Model #1500325A, що може через USB - підключати деякі мультимедійні пристрої: LPT (PL-2305H), COM (PL-2303HX), SOUND 5.1 (C-Media Electronics, Inc. Storm HP-USB500 5.1 Headset), Ehternet (ASIX AX88772), USB Hub.  
  

[![](/assets/images/blog/80603912b90c3a0b-5a8d856e95401372.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEga-Pkr6zGFwnUhEJmV9274uqZr1CeOKXSqSV7o3VvWe7poIsQfsu2CWfVxRsoohFmgAgX_3wohLeRKtB39lRvKzoWzEQV917tIjdx0ocHBIdC3lAeSlsLNa_zl7VxjEE-tB9jMhe_kTVlu/s1600/IMG_7577.JPG)  
*Notebook Dock Model #1500325A*

  

[![](/assets/images/blog/50016aed45dcf3c7-4fea40a817a62175.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjeh87CxcBs0hpQbuwaZg7G_q1VFCTaFVRRWW37HDLv1vevYNS3ZFfzgczVvJ5IUl6k3YCMkELlXeSooe7pnAB6mUw6dQvrkPVI7z-wT7lZ2B-F2ob_zJkv5PgIsKxvw4ylZSqEKnngzTda/s1600/IMG_7578.JPG)  
*Notebook Dock Model #1500325A*

  

[![](/assets/images/blog/b8dfc1dcc9cdaffc-e6c4804e88388aa1.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh256wVmFTE3g6wnTIZiFM3tkbkwWmfijqXMwGdwWD-QleXb5B08UnFvp-PA9ifguhOZMUFU8Don0L-CjukmZsdZM2uw_kCnqhSqHIxnrb7_0KH5tRp2Suj9vyniRCxQDZwUtJFa-b047xs/s1600/IMG_7580.JPG)  
*Notebook Dock Model #1500325A,PL-2303HX. PL-2305H*

  
  
  
  
opkg install usbutils, lsusb  

root@OpenWrt:~# lsusb

Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub

Bus 001 Device 002: ID 0409:0059 NEC Corp. HighSpeed Hub

Bus 001 Device 003: ID 0409:0059 NEC Corp. HighSpeed Hub

Bus 001 Device 007: ID 0b95:7720 ASIX Electronics Corp. AX88772

Bus 001 Device 008: ID 067b:2305 Prolific Technology, Inc. PL2305 Parallel Port

Bus 001 Device 009: ID 067b:2303 Prolific Technology, Inc. PL2303 Serial Port

Bus 001 Device 010: ID 0d8c:0006 C-Media Electronics, Inc. Storm HP-USB500 5.1 Headset

  
  
За відтворення звуку відповідає пристрій "C-Media Electronics, Inc. Storm HP-USB500 5.1 Headset"  
opkg install kmod-usb-audio  - Kernel support for USB audio devices  
Залекжить від kmod-usb-core, kmod-sound-core  
  
root@OpenWrt:~# dmesg |grep audio  
usb\_audio: Warning! Unlikely big volume range (=8191), cval->res is probably wrong.  
usb\_audio: [11] FU [Line Playback Volume] ch = 2, val = 0/8191/1  
usbcore: registered new interface driver snd-usb-audio  
  
[ <http://wiki.openwrt.org/doc/howto/usb.audio> ]  
  

### Відтворюємо звук через USB AUDIO

Для відтворення беремо потік від трансляції Інтренет радіо, наприклад "[Є Радіо!](http://eradio.net.ua/)".  
Пряме послання на [playlist](http://eradio.net.ua/eradio_hi.m3u) , завантаживши його я знайшов пряму адресу до трансляції "http://62.149.13.129:8500/eradio\_hi". Потік отримаємо за допомогою wget, а програємо через madplay.  
  
opkg install madplay - MAD is an MPEG audio decoder. It currently only supports the MPEG 1  
 standard, but fully implements all three audio layers (Layer I, Layer II,  
 and Layer III, the latter often colloquially known as MP3.). There is also  
 full support for ID3 tags.  
  
  
Ствоюю най простіший скрипт для запуску програвання радіостанціїї у фоні /etc/radio.sh:  
wget -c -q -O - http://62.149.13.129:8500/eradio\_hi | madplay -q - &  
Ствоюю скрипт для зупинки програвання радіостанціїї /etc/killradio.sh:  

killall wget

  
Звучання Є!!! Слухаю раді і зараз коли пишу цю публікацію.  
Навантаження на процесор досить велике, 17-48%.    
Так можна вмикати або вимикати радіо [клавішею роутера](http://wiki.openwrt.org/doc/howto/hardware.button).  
  
[ <http://wiki.openwrt.org/doc/howto/usb.audio> ]  
  

### USB Ehternet

lsusb:

Bus 001 Device 007: ID 0b95:7720 ASIX Electronics Corp. AX88772

  
opkg install kmod-usb-net-asix - Kernel module for USB-to-Ethernet Asix convertors  
залежить від kmod-usb-net  
  

root@OpenWrt:~# dmesg |grep asix

asix 1-1.1.1:1.0: eth1: register 'asix' at usb-ehci-platform-1.1.1, ASIX AX88772 USB 2.0 Ethernet, 00:50:b6:XX:XX:XX

usbcore: registered new interface driver asix

  
  

### Samba

  
Samba - працює, для дозволу на запис через samba не забуваємо дати дозвіл до теки наприклад, /mnt/usb: chmod -R 777 /mnt/usb  
  
/etc/config/fstab:   
  
config mount  
        option options 'rw,codepage=866,iocharset=cp1251,umask=000,dmask=000,fmask=000,uid=65534,gid=65534'  
        option fstype 'vfat'  
        option uuid 'E6D6-620A'  
        option target '/mnt/multiboot'  
        option enabled '0'  
Швидкість запису на USB HDD під'єднаного до TP-LINK 1043 становила біля 4.2-5 Mб/с (FAR copy)   
  
Завантаження процесора:   
  PID  PPID USER     STAT   VSZ %VSZ %CPU COMMAND  
15618 15201 nobody   R     3768  13%  49% /usr/sbin/smbd -D  
  481     2 root     RW       0   0%   4% [usb-storage]  
    3     2 root     SW       0   0%   4% [ksoftirqd/0]  
 5304     2 root     SW       0   0%   3% [jbd2/sdb2-8]  
12257     2 root     SW       0   0%   2% [kworker/0:2]  
  479     2 root     SW       0   0%   1% [usb-storage]  
  
Читання з USB HDD під'єднаного до TP-LINK 1043 становила біля 6-7 Мб/с, на SSD накопичувач (FAR copy).  
Завантаження процесора:   
  PID  PPID USER     STAT   VSZ %VSZ %CPU COMMAND  
15618 15201 nobody   R     3768  13%  56% /usr/sbin/smbd -D  
  481     2 root     SW       0   0%  13% [usb-storage]  
12311     2 root     SW       0   0%   5% [kworker/u:1]  
12257     2 root     SW       0   0%   5% [kworker/0:2]  
  479     2 root     RW       0   0%   4% [usb-storage]  
   95     2 root     DW       0   0%   1% [kswapd0]  
  

### NFS

Перевірив, досить повільно працює у зв'язці Windows 7, NFS componets, mount .  
Швидкість запису на USB HDD під'єднаного до TP-LINK 1043 становила біля 750-840Кб/с (FAR copy)  
Завантаження процесора:   
PID PPID USER STAT VSZ %VSZ %CPU COMMAND  
7827  2 root DW 0 0% 18% [nfsd]  
 7825  2 root SW 0 0% 14% [nfsd]   
481   2 root SW 0 0% 10% [usb-storage]  
5304  2 root DW 0 0% 10% [jbd2/sdb2-8]  
3     2 root SW 0 0%  5% [ksoftirqd/0]  
10408 2 root SW 0 0%  2% [kworker/0:2]  
482   2 root SW 0 0%  1% [kworker/u:2]  
  
  
  
Читання з USB HDD під'єднаного до TP-LINK 1043 становила біля 4.2-4.8 Мб/с, на SSD накопичувач (FAR copy).  
Завантаження процесора:   
PID PPID USER STAT VSZ %VSZ %CPU COMMAND  
 7825     2 root     SW       0   0%  19% [nfsd]  
 7826     2 root     RW       0   0%  15% [nfsd]  
  481     2 root     DW       0   0%  11% [usb-storage]  
11610     2 root     SW       0   0%   8% [kworker/0:1]  
 7823     2 root     SW       0   0%   7% [nfsd]  
 7828     2 root     SW       0   0%   5% [nfsd]  
    3     2 root     SW       0   0%   4% [ksoftirqd/0]  
  482     2 root     SW       0   0%   3% [kworker/u:2]  
  
  
  
  

### 

### Блокування з'єднань

Блокую користувачів 192.168.0.128/28 (пристрої моїх дітей) за розкладом на ніч, та на час для стимуляції виділення часу на навчання.  
  
#!/bin/sh  
echo disable users by firewall... |logger -t 'firewall';  
#deny users  
/usr/sbin/iptables -I zone\_lan\_forward -j zone\_wan\_REJECT -s 192.168.0.128/28  
  
grep ESTABLISHED /proc/net/nf\_conntrack | cut -d "=" -f 2| cut -d " " -f 1|sort -u | while read ip4; do \  
 ip44=$(echo $ip4 | cut -d "." -f 4); \  
 if [[ $ip44 -ge 128 && $ip44 -le 148 ]]; then \  
   /usr/sbin/cutter $ip4 >/dev/null; \  
 fi \  
 done  
  
/proc/net/ip\_conntrack  замінено на  /proc/net/nf\_conntrack, це у
мене використовується при визначені поточних з'єднань котрі треба
"обрізати" при блокуванні користувача за часом.  
Але у цьому транку r33206 у пакунку cutter, ще використовується посилання на /proc/net/ip\_conntrack, і тому пакунок не працює.  
  
  
p.s. після мого подданя tickets про цю помилку, cutter було виключенно з пакунків транку :(   
  

### Wifi канали 12-14

Згідно з правилами регулювання радіочастот у країнах присутні обмеження для різних країн по використанню каналів Wi-Fi, та максимальних потужностей передавача.  
Так наприклад, US    
  
*# iw reg set US*  
*#dmesg*   
 cfg80211: Calling CRDA for country: US  
 cfg80211: Regulatory domain changed to country: US  
 cfg80211:   (start\_freq - end\_freq @ bandwidth), (max\_antenna\_gain, max\_eirp)  
 cfg80211:   (2402000 KHz - 2472000 KHz @ 40000 KHz), (300 mBi, 2700 mBm)  
 cfg80211:   (5170000 KHz - 5250000 KHz @ 40000 KHz), (300 mBi, 1700 mBm)  
 cfg80211:   (5250000 KHz - 5330000 KHz @ 40000 KHz), (300 mBi, 2000 mBm)  
 cfg80211:   (5490000 KHz - 5600000 KHz @ 40000 KHz), (300 mBi, 2000 mBm)  
 cfg80211:   (5650000 KHz - 5710000 KHz @ 40000 KHz), (300 mBi, 2000 mBm)  
 cfg80211:   (5735000 KHz - 5835000 KHz @ 40000 KHz), (300 mBi, 3000 mBm)  
  
  
  
  
*# iwlist  wlan0 channel*  
wlan0     11 channels in total; available frequencies :  
          Channel 01 : 2.412 GHz  
          Channel 02 : 2.417 GHz  
          Channel 03 : 2.422 GHz  
          Channel 04 : 2.427 GHz  
          Channel 05 : 2.432 GHz  
          Channel 06 : 2.437 GHz  
          Channel 07 : 2.442 GHz  
          Channel 08 : 2.447 GHz  
          Channel 09 : 2.452 GHz  
          Channel 10 : 2.457 GHz  
          Channel 11 : 2.462 GHz  
  
*#iw reg set UA*  
*#dmesg*  
 cfg80211: Calling CRDA for country: UA  
 cfg80211: Regulatory domain changed to country: UA  
 cfg80211:   (start\_freq - end\_freq @ bandwidth), (max\_antenna\_gain, max\_eirp)  
 cfg80211:   (2402000 KHz - 2482000 KHz @ 40000 KHz), (N/A, 2000 mBm)  
  
У транку є данні тільки для US  
За данні  по частотам відповідає пакунок crda  
*# opkg install crda*  
  
Змінити данні  по частотам можна використавши метод <http://wiki.openwrt.org/toh/netgear/wndr3700>. завантаживши модифікований файл regulatory.bin та виконавши команди  
  

```
#cd /usr/lib/crda/ 
#rm regulatory.bin
#wget <web server URL>/regulatory.bin
#reboot
```

  
[Ось новий метод для дозволу використання WiFi каналів 12,13](http://www.lexxai.pp.ua/2013/02/openwrt-attitude-adjustment-wifi-1213.html)  

### Перший порт для підключення IPTV приставки чи комп'ютера

Конфігурація через графічний інтерфейс, Network, Switch, цікаві тут тільки перші 2 рядки, vlan1 - локальна мережа, та vlan2 - мережа інтернет.  

[![](/assets/images/blog/2e5311eca26c0872-5a8bbad146f6e345.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh25JiZlnU2NxlUBspJZ2t-gD7KS5nGQ8_W16MoghFbpDcyIFkq-wpuDbjvmKs2cutPTRv7FWNunQ267HlIm7eXLjctTzYE2RIogyn2EvLrjTg-ErExYtzkdT6rPctnRYW-ZuVS020Qj6hf/s1600/openwrt-vlan-iptv.PNG)  
*порт для підключення IPTV приставки чи комп'ютера*

Це зображення відповідає файлу конфігурації /etc/config/network  
  
config switch  
        option name 'rtl8366rb'  
        option reset '1'  
        option enable\_vlan '1'  
        option enable\_vlan4k '1'  
  
config switch\_vlan  
        option device 'rtl8366rb'  
        option vlan '1'  
        option ports '2 3 4 5t'  
  
config switch\_vlan  
        option device 'rtl8366rb'  
        option vlan '2'  
        option ports '0 1 5t'  
  
config switch\_vlan  
        option device 'rtl8366rb'  
        option vlan '202'  
        option ports '4t 5t'  
  
config switch\_vlan  
        option device 'rtl8366rb'  
        option vlan '203'  
        option ports '4t 5t'  
  
config switch\_vlan  
        option device 'rtl8366rb'  
        option vlan '204'  
        option ports '4t 5t'
