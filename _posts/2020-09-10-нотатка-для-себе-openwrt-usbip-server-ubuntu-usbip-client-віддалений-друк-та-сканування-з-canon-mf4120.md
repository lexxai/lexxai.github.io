---
layout: post
title: "Нотатка для себе: openwrt usbip server, ubuntu usbip client, віддалений друк та сканування з Canon MF4120"
date: 2020-09-10 17:14:00 +0000
tags: ["asus", "canon", "cups", "gimp", "Open Source", "open-wrt", "p910nd", "pixma", "print", "scan", "scanimage", "ubuntu", "usb over ip", "usbip"]
blogger_orig_link: https://lexxai.blogspot.com/2020/09/openwrt-usbip-server-ubuntu-usbip.html
---

Маю з старого нетбуку Asus 1001PX зроблений WiFi принт-сервер на основі OpenWRT.

[![](/assets/images/blog/38ef4e35dff8eb98-7ff3c89704ce4f3b.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhFFKGN_wesLfUYPGEn5J2ZuMlmTB7yKkpnH6pmxnhrUdsx2qn6PDhcTYx5f4QgZciKbU7VlvwQa9iU59pgUbKVNdK9o7R3nrjKqUm4tXjLPbb6fHjMcWJ50_W6pGsfS6kHHFIh8Wk4CJAA/s4608/IMG_20200910_213806.jpg)  
*Старий Asus 1001PX та Canon MF4120*

  
 Так як процесор Intel х86 і  пристрої мережевих карт не включенні з базову збірку в дистрибутиві, то я компілював власний образ  записав його на SD карту. Ноутбук без HDD, стартує з SD карти.

[![](/assets/images/blog/55e23f563b0637ec-807be4e0359a2f18.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh-b_R2T67Csjrbsimm2IFc63ezUanmjPqrQGjAHCuEf06kAcU7_FdRuHaEGXlZGCRbSC4VKRMSlKq4LA7Syb_KdViETVasrFU6g1kDtwqzfpLrYW9ZQ1HqhgrdnEcALIYuhBQTGbHbTuil/s976/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-09-10+18-30-28.png)  
*OpenWRT ASUS 1001PX*

Друк працює через стандартний сервіс p910nd

[![](/assets/images/blog/2dc4d0b0bf3e1b3a-794751875005b4fc.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiJYHZv4Oyv-oDtYGI5bjbIrvA1qCShemdAbs93AYyfyVI8PVMGGll3qHi62U7sq4A47clbJc_4IJ4WVe2cNL1oaQw0znjtZuNV4dqRXNQVYuYL97xEtr1OrimHZJ1Rr4fzSthFkf3GkO3A/s967/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-09-10+18-32-08.png)  
*сервіс p910nd*

Принтер "БФП Canon-MF4120" підключений через USB.

Все працює як мережевий друк з стаціонарних комп'ютерів Ubuntu / Windows, для того щоб друкувати  з мобільних пристроїв були спроби розвернути [сервіс cups](https://openwrt.org/docs/guide-user/services/print_server/cups.server), але не вдалий був експеримент, тому закинув цю ідею.

Давно теж пробував використати сервіс [USB over IP.](https://openwrt.org/docs/guide-user/services/usb.iptunnel)

Тому спробував зараз:

opkg update  
opkg install kmod-usb-ohci usbip-server usbip-client

Але за командою usbip list -l було пусто. За [рішеннями](https://habr.com/ru/post/504338/) інших користувачів спробував використати  замість бібліотеки libudev-fbsd з поточної версії, використати бібліотеку libudev з попередньої версії:

wget http://downloads.openwrt.org/releases/17.01.7/packages/i386\_pentium4/packages/libudev\_3.2-1\_i386\_pentium4.ipk

opkg remove libudev-fbsd   
opkg install libudev\_3.2-1\_i386\_pentium4.ipk  
reboot

Тепер usbip list -l видала результат:

[![](/assets/images/blog/e64df0a5d4af4e7d-90eb66953a9cd0d7.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjCxDP1A6yYMeZZF-X_89PQbLFBHz-E6xWozHxB4I8AsLx9aJ4LA5NZmiIwoKERXeQdi9EhGkQ2O_3EBTK9JuZ6H7FP6oSwH77kykNsDP0uui7doPx7YKzlU6WI4HahcDNNYf-ft-RUfKzm/s565/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-09-10+19-09-40.png)  
*Результат usbip list -l*

  

usbipd -D

usbip bind -b 1-1   
usbip: info: bind device on busid 1-1: complete

### Ubuntu 2004

$usbipd  
  
Команда «usbipd» не знайдена, але може бути встановлена ​​з:  
  
sudo apt install linux-oem-5.6-tools-common  # version 5.6.0-1017.17, or  
sudo apt install linux-tools-common          # version 5.4.0-47.51

$sudo apt install linux-tools-common

$ usbipd  
WARNING: usbipd not found for kernel 5.4.0-42  
  
  You may need to install the following packages for this specific kernel:  
    linux-tools-5.4.0-42-generic  
    linux-cloud-tools-5.4.0-42-generic  
  
  You may also want to install one of the following packages to keep up to date:  
    linux-tools-generic  
    linux-cloud-tools-generic

$ sudo apt install linux-tools-5.4.0-42-generic

$ sudo usbip list -r printer.lan  
usbip: error: failed to open /usr/share/hwdata//usb.ids  
Exportable USB devices  
======================  
 - printer.lan  
        1-1: unknown vendor : unknown product (04a9:26a3)  
           : /sys/devices/pci0000:00/0000:00:1d.7/usb1/1-1  
           : (Defined at Interface level) (00/00/00)

$ sudo usbip attach -r printer.lan -b 1-1  
libusbip: error: udev\_device\_new\_from\_subsystem\_sysname failed  
usbip: error: open vhci\_driver

Рішення [було знайдено тут](https://unix.stackexchange.com/questions/470827/usbip-error-open-vhci-driver).

$sudo modprobe vhci-hcd

$sudo usbip attach -r printer.lan -b 1-1

Перевіряємо:

$sudo usbip  port  
usbip: error: failed to open /usr/share/hwdata//usb.ids  
Imported USB devices  
====================  
Port 00: <Port in Use> at High Speed(480Mbps)  
       unknown vendor : unknown product (04a9:26a3)  
      11-1 -> usbip://printer.lan:3240/1-1  
           -> remote bus/dev 001/002

$lsusb  
Bus 011 Device 004: ID 04a9:26a3 Canon, Inc. MF4100 series

І система автоматично встановила знайлений принтер:

[![](/assets/images/blog/8d726038b72bc6e8-9fb2beca43e00943.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgZ5hByNYy0HU0Cjb90Yh5Gv58c8fzNJ2ReoxoJMjV7FsIbJG8spDyehzb_53ONJsxsAHYGj5JKAaICUR8WxxHMQdhuh_u8y9ClxuYsmyDP2uy2LpBqMEgJxFqLuHU9lceA8ROcqGp8xcx_/s973/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-09-10+19-23-52.png)  
*Принтер через USB over IP*

  
Після зупинки служби p910nd, принтер надрукував сторінку через USB over IP.

Якщо винклаи проблкми з vhci-hcd то можна вивантажити і завантажити:

$sudo modprobe -r vhci-hcd  
$sudo modprobe vhci-hcd

[![](/assets/images/blog/826590cd4ff5a617-d1a14060141d9266.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiTqORYK40EYDujOB6Cu55nF_5x9Ht5BOr0nfFqRvc6ENJgf8vqkSTgBQHsV1Bj3ktSARUaQwPkZxphEE5KHnIpOHHeSISqYAewSn3eSAphiBPgUcYSePf8Rf7OlJBgHR-z2SraODUb7I0I/s1024/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-09-10+20-00-33.png)  
*результат dmesg*

  

### Сканер

$scanimage -L  
device `pixma:04A926A3\_SDF760240377O' is a CANON Canon imageCLASS MF4150 multi-function peripheral

[![](/assets/images/blog/005bc71a44198955-ae3f9a03687bdc75.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhpLgX92bp8nEf6QEhtdUKyZPo6xhE-ba2nANlUpaU80eTd8oS_KSNk8CM4CaHbPkXIUpsgKUv4ctUF7YqNbIqOj1jzW9wl0o9OYbljeWg18xCuleCUPHNMM7nnPPfyjI9tLOyDzlPzo3k2/s1015/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-09-10+20-03-58.png)  
*Попередній пергляд при скануванні*

  

[![](/assets/images/blog/e016a8c0e415e73b-2d9e41e35e9280b4.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg5Uc9PyFBl46Im-anI9uVZG1cFRBxCzQUNw7Z7chv7CYkyBzftJHR8FOjTlVGbhAoSOGYaZ7Q8zMVND0edEfiOUh8ZycdFTql4W9Rgbc4UcDnFNujbXLpuWJ7ngmwP1HTLrAUqjJCKX_zs/s1226/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2020-09-10+20-09-18.png)  
*Результат сканування 600 dpi.*
