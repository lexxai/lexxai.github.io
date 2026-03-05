---
layout: post
title: "Mulit-SSID Wi-Fi та VLAN у роутері TP-Link 1043 (OpenWRT)"
date: 2012-07-07 21:06:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2012/07/vlan-tp-link-1043.html
---

Прийшов час трохи розібратися з начинкою свого роутера більш детально у тій сфері що не використовував - VLAN.  
  
Для ознайомлення почав тут:   

* [VLAN — Xgu.ru](http://xgu.ru/wiki/VLAN)
* [Комутатор і VLAN з swconfig WR1043ND на OpenWrt](http://translate.googleusercontent.com/translate_c?rurl=translate.google.com&sl=auto&tl=ua&twu=1&u=http://rpc.one.pl/index.php/lista-artykulow/34-openwrt/81-konfiguracja-switch-vlan-na-podstawie-swconfig-w-routerze-wr1043nd-pod-openwrt&usg=ALkJrhg0x8UTqTncS_rQqIFwZiEqvfYJaw)
* [Multiple SSID with VLAN](http://trizelka.wordpress.com/openwrt-multiple-ssid-with-vlan)
* [Multiple SSID on OpenWRT with bandwidth limit](http://jwalanta.blogspot.com/2012/03/multiple-ssid-on-openwrt-with-bandwidth.html)

  

[![](http://rpc.one.pl/images/stories/images/wr1043nd/vlan_wr1043nd.jpg)](http://rpc.one.pl/images/stories/images/wr1043nd/vlan_wr1043nd.jpg)  
*Архітектура WR1043ND*

  
  
  
  
Хтось робив tagged пакети VLAN, з метою виділяти multi WiFi мережі?  
 Наприклад, так робить моя точка доступу D-Link DWL 2100:  
 Є [Mulit-SSID wifi](http://network.xsp.ru/5_11.php) мережі some202, some203, some204 і їм відповідно призначаються VLAN ID  202, 203, 204. IP пакети, що стосуються some202, some203, some204, мітяться (tagged) спеціальними мітками.  

```
Index       SSID   Band Encrypt. VLAN ID  
Primary     some202 11g  WPA2-PSK 202
Multi-SSID1 some203 11g  WPA2-PSK 203 
Multi-SSID2 some204 11g  WPA2-PSK 204
```

Потім на сервері, ці VLANs, розбираються на свої віртуальні мережі, на основі мічених пакетів (tagged) .  
  
Як таке зробити на OpenWRT?  
 Ось тема на подібну тему <https://forum.openwrt.org/viewtopic.php?id=30955>  
  
 Протеговані (tagged) пакети від Mulit-SSID WiFi, повинні бути на порту #4.  
  
Створюю VLANs на switch 'device' 'rtl8366rb', включивши до них тільки порт #4, та порт CPU #5.  
Пакети що виходять з цих портів будуть протегуватися.  
*/etc/config/network:*   
config 'switch\_vlan' #appended   
  option 'device' 'rtl8366rb'   
  option 'vlan' '202'   
  option 'ports' '4t 5t'   
  
config 'switch\_vlan' #appended   
  option 'device' 'rtl8366rb'   
  option 'vlan' '203'   
  option 'ports' '4t 5t'   
  
config 'switch\_vlan' #appended   
  option 'device' 'rtl8366rb'  
  option 'vlan' '204'   
  option 'ports' '4t 5t'   
  
Так як порт 4 використовуєтеся виключно для VLAN WiFi Multi-SSID, виключаємо його з  VLAN 1.   
*/etc/config/network:*   
config 'switch\_vlan'   
  option 'device' 'rtl8366rb'   
  option 'vlan' '1'   
  option 'ports' '1 2 3 5t' #moved port 4 to vlans 202-204   
  
config 'switch\_vlan'   
  option 'device' 'rtl8366rb'   
  option 'vlan' '2'   
  option 'ports' '0 5t'   
  

[![](/assets/images/blog/a81f7952cbd5273d-d01413609a9160d8.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiMi4Pej1GF_VmX0qIwEqhwPkGtmgyI6h5oy73BRDm981snivxq-naviVZWDcAY0D5dSug2MgsaNAAL9jwAftVBLJr7zEmRL4oWX4kX08ABdPaR74GlYSuhdZwr4ypudOjeaDj4QaWVDBO5/s1600/vlan-01.png)  
*LuCI switch config*

Потім потрібно створити віртуальні мережі 202, 203, 204 на базі реального інтерфейсу eth0, де номер віртуальної мережі додається як суфікс до назви інтерфейсу через крапку. Кожна віртуальна мережа визначається своїм мережевим сегментом та унікальною статичною адресою для роутера.  
VLAN ID   SUBNET            Router IP   
vlan 202, 192.168.202.0/24, 192.168.202.1  
vlan 203, 192.168.203.0/24, 192.168.203.1  
vlan 204, 192.168.204.0/24, 192.168.204.1    
  
*/etc/config/network:*  
config 'interface' 'vlan\_202' #appended   
  option 'ifname' 'eth0.202'   
  option type 'bridge'   
  option 'proto' 'static'   
  option 'ipaddr' '192.168.202.1'   
  option 'netmask' '255.255.255.0'   
  
config 'interface' 'vlan\_203' #appended   
  option 'ifname' 'eth0.203'   
  option type 'bridge' option 'proto' 'static'   
  option 'ipaddr' '192.168.203.1'   
  option 'netmask' '255.255.255.0'   
  
config 'interface' 'vlan\_204' #appended   
  option 'ifname' 'eth0.204'   
  option type 'bridge' option 'proto' 'static'   
  option 'ipaddr' '192.168.204.1'   
  option 'netmask' '255.255.255.0'
  
А потім створюю підключення WiFi`s з ssid: "some202", "some203", "some204"  
і також створюю міст (bridge) між підключенням WiFi`s та відповідними VLANs: "vlan\_202", "vlan\_203", "vlan\_204".  
*/etc/config/wireless:*  
config wifi-iface   
  option device 'radio0'   
  option network 'vlan\_202'   
  option mode 'ap'   
  option ssid 'some202'   
  option encryption 'psk2+ccmp'   
  option key 'somepassword'   
  
config wifi-iface
option device 'radio0'   
  option network 'vlan\_203'   
  option mode 'ap'   
  option ssid 'some203'  
  option encryption 'psk2+ccmp'  
  option key 'somepassword'   
  
config wifi-iface   
  option device 'radio0'   
  option network 'vlan\_204'   
  option mode 'ap'
option ssid 'some204'   
  option encryption 'psk2+ccmp'   
  option key 'somepassword'  
  

[![](/assets/images/blog/5ad47231fccaf539-8b044d50dc385fc2.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhXUO0hAfAu_Op7LjHX8_TjTacEcLjLvsya4DiQSOkNghdjEFG22x6zrciwPph5q8wk8EukG5vrnVm2-umav9BF1baCYDxUz5NSk35pbc_CX6n9XNyyFEn1m6snvtUS0WUBR8A8CtBhWayo/s1600/vlan-02.png)  
*LuCI, WiFi 'Some20'2 bridged to 'VLAN\_202'*

  
Налаштовую DHCP на VLAN\_202, VLAN\_203, a VLAN\_204 спеціально залишаю без DHCP.  
*/etc/config/dhcp:*  
  
config dhcp  
  option start '100'  
  option leasetime '12h'  
  option limit '150'  
  option interface 'VLAN\_202'  
  
config dhcp  
  option start '100'  
  option leasetime '12h'  
  option limit '150'  
  option interface 'VLAN\_203'   
  
  
Потім до порту #4 треба підключити сервер і дивитися там проходження ip пакетів командою 'tcpdump -een' , на присутність ethertype 802.1Q пакетів від клієнтів, які підключилися по відповідних WiFi.  
  
  

### Перевіряємо VLAN на порту #4

### [WinDupm](http://www.winpcap.org/)

WinDump.exe -D - перелік адаптерів, шукаємо адаптер що під'єднано до порту #4  
WinDump.exe -i 1 -een  
У мене не показує ethertype 802.1Q, можливо драйвер не пропускає їх, у цієї мережевої карти є своя реалізація VLAN можливостей.  
Але коли я до порту  #4 під'єднав ноутбук з ОС Ubuntu, то tcpdump -i eth0 -een почав показувати    
ethertype 802.1Q.   
  
OS Windows: створимо віртуальні мережі на базі мережевого адаптера "Marvel Yukon 88E8056"  
  

[![](/assets/images/blog/ccdd05670d9e38ec-c4e36e7f10b82acc.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhW6mgTGBRMPSu0NKB22Zq9snTPMx4kSDm5bMTbMrF0VhDtYGxBihnziaLpkbiYCRDduA4KNDfkZRZiTLVldGyc4O4qTn5cNRGQ9_3mEWRGrPrjqZIUpPTHwX_JSS5Fm0ivNLx3PA5o4BIp/s1600/vlan-03.png)

  

[![](/assets/images/blog/62d0fcd3ed974e1e-819c80fcb36eb7dd.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg6CvlevjDqKxd3O7G2HZDy_yafhp_BQFXyNPc45WBi-TF6DzsyZeE78VK5rYnzhblHC-LCAQb_h0RZ4E0RjZzb6ghuDhx9aA2Ydw38nWK4ux7M_T-thVXMBzppSPebLxR4bYE1bbGk8CQN/s1600/vlan-04.png)

  

[![](/assets/images/blog/96f6303a994852e5-d5ab31e75f3a28f6.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEii-R31tQ7eqhBiehormFClf0TQr2NRE_lF9kpIFCgrJBxt76CWYDmsE-0Rbvyc96LT7u5_x260bF8UnqixJxeMSpWFO6UiZ8qbcLGzCSURtcXobF6fov6naG33R9qUzIhO1w8V9hj3Sh5g/s1600/vlan-05.png)

  

[![](/assets/images/blog/12397fefd10b1d14-3de7d8bd89e5dccf.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg_xse-FD9Kn4qx5vf3u5_hXzIAKiMy3nGPCK4T5WGgtduKb3yDAArhrCj49bj_3XJFlDRFT1-N5wET5j7AoOi0qw1H9jSJ1blkfUwfSA_u1My81F-XGfSX6uE0hoNJiybMI9H87eVsfQXL/s1600/vlan-06.png)

  

[![](/assets/images/blog/1b9fed29fe9a053e-38c4d2fc1a4ce1ab.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgc5ddjHaBHW-KLzWSaOKQ0cF2MDMSzeOKK9H54QrvCdVOlT7C5AAWgF2ziV0vU3MrbQnLdq3ypR2Qda5mySxtKdIqjuIZg1_DI_SlsCmI1HV5b7h_y4KjfS1EoWiDli8ooj_uC_Xy-j8dZ/s1600/vlan-07.png)  
*адаптери для зручності були перейменованідо VLAN\_202, VLAN\_203 та VLAN\_204*

  

[![](/assets/images/blog/b8902fd52bfdf3da-e56069d8c472424d.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi5oRZhmjXxWdvFbrh2W9xiAoGdue1ZFd_m6E06kUbIDQLOs6bZWBxDq1xclUSNBJ3YBB7tAJ1Ob3HVo3sCC63d3f9yCkt6oWbJ2w-oF3Mo3RNVpNZ3HfWFCM5NbE93Dy3qsZDi36lCDud-/s1600/vlan-08.png)  
*адаптер VLAN\_202 по DHCP взяв адресу 192.168.202.230*

  

[![](/assets/images/blog/48d49fc0a36563df-b43b37c9a3c6b95e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEius-l45u1SgqCPrS-LtAwJZIF1V8noozXXNxWTTwcNf8AwkzqrmIjXgUcVoUkIpjANxYCunYcWhj5KjwCV3-iK6NEpS9IhROlxWjAASGg4T17LPwJT1tGCA6rO7UOnenrRXa2t4iJb6sXn/s1600/vlan-09.png)  
*адаптер VLAN\_203 по DHCP взяв адресу 192.168.203.230*

  

[![](/assets/images/blog/f57d97ef08adc1b0-354d6420a4114739.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgCmMeJGMtuIipajwsBFilNNtGvxDFvIBF8n9PoOl7KyfG-GgMWVrKSOKBETmH_pmOgTdI1BkcicDcOxZZUnwVz_C1H_oY8HYjQkSsCWm2Y3OR2fFY7umKswaor9_ktMwniqDvLKTNW48ns/s1600/vlan-10.png)  
*адаптер VLAN\_2034 по DHCP не взяв адресу,йому призначено автоматичну 169.254.119.201*

З вище зазначених прикладів видно, що кожний адаптер зі своєю віртуальною мережею, взяв IP адресу по DHCP, що була налаштована в роутері на відповідному інтерфейсі eth0.202 та eth0.203. Інетефейс eth0.204 має статичну адресу і сервер DHCP не налаштований на цьому інтерфейсі, тому і адаптер VLAN\_204 по DHCP не взяв адресу. Але якщо ми пропишемо статичну адресу адаптеру VLAN\_204, наприклад 192.168.204.10/24, то зможемо зробити ping до роутера.  
  

[![](/assets/images/blog/e158c9be2725fa9a-b93e88e6e15cf8c4.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhf5_TzwGms7V4vvtEc5iBv3iSa5UIDdK2zsVGR_P7u_D5ZXzqLDl1xRRORsQeQEUPxAu9fb3yalb0GOPbcKT5zpzVY9Zp5PMChb73E9AZ8UVR11dNTgmAeWrsCnSD1CE-pZsrod1EKeGGy/s1600/vlan-11.png)

  

[![](/assets/images/blog/4c08850cea3fbeaf-d775cee31b5ab630.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjrY_MnIuAbrTK3vvTT8Am6vk7_1PTcTmMmioHTrO65bwfqYRowZCnggFIsYOW9shsPFxOf0DtnklUTq_wnmRMdljzytg78wzQjcuQZdGx_jGxsv30we0p1c1nB1MKCoXq3kohzerJNGWUC/s1600/vlan-12.png)

  
В свою чергу я приєднав до WiFi мережі 'some202',  notebook.  
Ноутбук взяв собі адресу по DHCP : 192.168.202.231.   
Зробив ping з notebook до роутера 192.168.202.1, і до адаптера PC (під'єднаний до порту 4, та налаштований VLAN 202) з адресою 192.168.202.230 що знаходяться в тій самій віртуальній мережі - успішно.   
Зробив ping з PC 192.168.202.230 до notebook 192.168.202.231 що знаходяться в тій самій віртуальній мережі - успішно.   
Зробив ping до 192.168.203.1 та 192.168.204.1 теж вдало, бо пройшло внутрішнє перетворення так як це один інтерфейс.   
Але до інших адрес з інших віртуальних мереж вже не вдалося:  
Зробив ping з
notebook 192.168.202.231 до адаптера з адресою
192.168.203.230 що знаходяться в віртуальній мережі VLAN\_203 - відповіді не має.  
Встановив статичну адресу ноутбуку коли він під'єднаний до WiFi 'some202' із "нерідної" VLAN - 192.168.203.231, спроба зробити ping до PC 192.168.203.230 - не вдача.  
  
  

### tagged & untagged

Наступна реалізація коли порт 4 використовується як для tagged так і untagged ip пакетів.  
  
config switch\_vlan  
        option device 'rtl8366rb'  
        option vlan '1'  
        option ports '1 2 3 4 5t'  
  
config switch\_vlan  
        option device 'rtl8366rb'  
        option vlan '2'  
        option ports '0 5t'  
  
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
  
config switch\_port  
        option device 'rtl8366rb'  
        option port '4'  
        option pvid '1'  
   
  
У такому випадку  switch\_port пере приділяє значення для пакетів що були untagged ip (звичайні ip), до фіксованого значення VLAN, у нашому випадку назначає до VLAN 1.  
І під'єднанний ноутбук по DHCP отримає адресу з мережі 192.168.0.0/24.  
Якщо прописати так:   
  
config switch\_port  
        option device 'rtl8366rb'  
        option port '4'  
        option pvid '203'  
І під'єднаний ноутбук до порту #4, по DHCP, отримає адресу з мережі 192.168.203.0/24  
  
Хочу зауважити що, switch\_port з визначенням pvid буде працювати для тих портів котрі у розділі switch\_vlan описні як untagged.  
Кожен порт може бути визначений як untagged, тільки один раз.  
  
  
Така конфігурація не буде працювати для untagged ip:   
config switch\_vlan  
        option device 'rtl8366rb'  
        option vlan '1'  
        option ports '1 2 3 5t'  
  
config switch\_vlan  
        option device 'rtl8366rb'  
        option vlan '202'  
        option ports '4t 5t'  
  
config switch\_port  
        option device 'rtl8366rb'  
        option port '4'  
        option pvid '202'  
  
  
P.S.   
  
Якщо виконати:   
*#swconfig dev rtl8366rb port 0 show |grep pvid*  
pvid: 2  
  
*#swconfig dev rtl8366rb port 1 show |grep pvid*  
pvid: 1  
  
*#swconfig dev rtl8366rb port 2 show |grep pvid*  
pvid: 1  
  
*#swconfig dev rtl8366rb port 3 show |grep pvid*  
pvid: 1  
  
*#swconfig dev rtl8366rb port 4 show |grep pvid*  
pvid: 204  
  
  
Бачимо якщо інформація іде 'untagged' то вона буде автоматично 'tagged' до відповідного PVID.  
По замовчувані  PVID відповідає VLAN ID, до якого призначений цей порт.  
У випадку  port 4, він присутій у декільках VLAN, у такому випадку береться останній VLAN ID.  
  
Якщо ми хочемо перевизначити  PVID то використовуємо config 'switch\_port'  
  
config 'switch\_port' #appended  
    option 'device' 'rtl8366rb'  
    option 'port' '4'  
    option 'pvid' '202'  
  
Тепер бачимо   
*#swconfig dev rtl8366rb port 4 show |grep pvid*  
pvid: 202  


  
  
  
P.S. P.S.  
Визначення switch через "LuCI Save and Apply", тільки зберігає значення до фалу конфігурації.  
Для за діяння змін треба запустити /etc/init.d/networks restart.  
  
  
  

## До нотаток:

### TCPDUMP

  
opkg install tcpdump  

#tcpdump -n -ee

21:59:55.294869 48:5b:39:XX:XX:c8 > 74:ea:3a:XX:XX:6a, ethertype 802.1Q (0x8100), length 110: vlan 1, p 1, ethertype IPv4, 192.168.0.2.50778 > 192.168.0.3.22: Flags [P.], seq 1041:1093, ack 202660, win 16118, length 52  
21:59:55.295486 74:ea:3a:XX:XX:6a > 48:5b:39:XX:XX:c8, ethertype 802.1Q (0x8100), length 58: vlan 1, p 0, ethertype IPv4, 192.168.0.3.445 > 192.168.0.2.50775: Flags [.], ack 1838141, win 12912, length 0  
  
пакети від vlan 1, p 1  до  vlan 1, p 0 при передачі файлів по SMB з PC до Samba server роутера.  
  
  
22:55:56.588364 74:ea:3a:XX:XX:6a > 00:12:fb:XX:XX:05, ethertype 802.1Q (0x8100), length 46: vlan 1, p 0, ethertype ARP, Request who-has 192.168.0.92 tell 192.168.0.3, length 28  
22:55:56.588558 00:12:fb:XX:XX:05 > 74:ea:3a:XX:XX:6a, ethertype 802.1Q (0x8100), length 64: vlan 1, p 1, ethertype ARP, Reply 192.168.0.92 is-at 00:12:fb:99:f5:05, length 46  
   
пакети від vlan 1, p 0  до  vlan 1, p 1 при ARP request  з роутера до TV  
  
22:42:11.519793
00:XX:66:XX:XX:1f > ff:ff:ff:ff:ff:ff, ethertype 802.1Q (0x8100),
length 64: vlan 2, p 1, ethertype ARP, Request who-has 27.27.22.27 tell
27.27.22.254, length 46   
пакети від vlan 2, p 1  при ARP request  з роутера WAN IPTV (підключений до port #1 роутера) до ff:...:ff  
  

23:06:48.335623 00:XX:66:XX:XX:1f > ff:ff:ff:ff:ff:ff, ethertype 802.1Q (0x8100), length 64: vlan 2, p 1, ethertype ARP, Request who-has 37.37.22.29 tell 37.37.22.254, length 46

  
пакети від vlan 2, p 1   при ARP request  з роутера WAN (підключений до port #0 [WAN] роутера) до ff:...:ff  
  
48:5b:39:XX:XX:c8 - PC (підключений до port #2 роутера)  
74:ea:3a:XX:XX:6a - Роутер з Samba Server  
00:XX:66:XX:XX:1f - WAN Роутера для IPTV (до port #1 роутера підключений IPTV player)  
00:12:fb:XX:XX:05 - TV (підключений до port #3 роутера)   
 */etc/config/network:*  
config switch  
        option name 'rtl8366rb'  
        option reset '1'  
        option enable\_vlan '1'  
  
config switch\_vlan  
        option device 'rtl8366rb'  
        option vlan '1'  
        option ports '2 3 4 5t'  
  
config switch\_vlan  
        option device 'rtl8366rb'  
        option vlan '2'  
        option ports '0 1 5t'   
  
  
  
Цією командою дивимося реальний стан:   
#swconfig dev rtl8366rb show  
...   
VLAN 1:  
 info: VLAN 1: Ports: '2345t', members=003c, untag=001c, fid=0  
 fid: 0  
 ports: 2 3 4 5t  
VLAN 2:  
 info: VLAN 2: Ports: '015t', members=0023, untag=0003, fid=0  
 fid: 0  
 ports: 0 1 5t  
  
  
#cat /proc/net/vlan/config  
VLAN Dev name | VLAN ID  
Name-Type: VLAN\_NAME\_TYPE\_RAW\_PLUS\_VID\_NO\_PAD  
eth0.1 | 1 | eth0  
eth0.2 | 2 | eth0  
  
  
#swconfig dev rtl8366rb vlan 1 get info  
VLAN 1: Ports: '2345t', members=003c, untag=001c, fid=0  
  
#swconfig dev rtl8366rb vlan 1 get ports   
2 3 4 5t
