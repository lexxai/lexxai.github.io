---
layout: post
title: "Планування правил firewall за часом у OpenWrt"
date: 2011-10-17 22:14:00 +0000
tags: ["cron", "cutter", "firewall", "firmware", "ip_conntrack", "iptables", "kill connenction", "openwrt", "router", "rules", "shedule", "TP-Link 1043"]
blogger_orig_link: https://lexxai.blogspot.com/2011/10/firewall-openwrt.html
---

Планування правил firewall за часом у OpenWrt не реалізовано графічним засобами.   
У оригінальному програмному забезпечені роутера це робиться досить просто. А якщо користувач не дуже знайомий з особливостями Linux то можливо виникають питання.  
  
Так є завдання - блокувати для IP адрес локальної мережі, за розкладом, вихід до мережі інтернет.  
У роутері попередньо налаштований DHCP pool на видачу адрес у діапазоні  192.168.0.128 - 192.168.0.142.   
  
***firewall***  
  
Для блокування створимо теку для зберігання скриптів, наприклад */etc/fw-cron*  
і там файли вмикання привил *child.1.sh* та вимикання правил *child.0.sh.*  

```
touch  /etc/fw-cron/child.1.sh
```

```
touch  /etc/fw-cron/child.0.sh
```

```
chmod +x /etc/fw-cron/child.*
```

  
  
  
У моєму випадку блокується вихід до мережі інтернет з адрес 192.168.0.128/28 (192.168.0.128 - 192.168.0.142)\*.   \* -на допомогу прийде [ipcalc](http://ipcalc.dp.ua/)  
тому зміст файлів наступний :  
  
child.1.sh:  

/usr/sbin/iptables -I zone\_lan\_forward -j zone\_wan\_REJECT -s 192.168.0.128/28

  
Цей запис вставляє, до фаєрволу iptables, правило за номером 1 у ланцюжок з іменем "zone\_lan\_forward", якщо адреса комп'ютерів підлягає під мережу 192.168.0.128/28, то правило направляє пакети до зони "zone\_wan\_REJECT". Та в свою чергу блокує вихід до WAN.  
  
Ось надано вигляд ланцюгів iptables що існують:  
  
  

[![](/assets/images/blog/1082b4e1fa7b9a0f-f7fbd8448a30f685.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiQMIFmmESg-ozSw9KuzBQPEaY7pMYr0Ng66_R1VDhO1VmzIUz_fCUzNvYADIQxhoLrSNtcqsCkE-XoYHVtiouv-QB9S2ncF_3Oq1ILeR43s30KqP_0csUdugvVwMivcPz-DiLuolHjH9OH/s1600/firewall-rule-01.PNG)  
*zone\_lan\_forward*

[![](/assets/images/blog/4cfdcb3f35460482-b877763416441711.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiOnuKoV9MkMea2LMYVPFsK7vJubntsyXDAkYj_TLLbaflTbh4MggY7tlBG0utF7rlwKLc6DWslUeEkzok_Q7ceIc9sWkvlyJW65LVMBpApCqYx5hm42S3ud_hsMypLMNGYCCUDux-W4ETC/s1600/firewall-rule-02.PNG)  
*zone\_wan\_REJECT*

[![](/assets/images/blog/fbfa902ef254633b-9028a70f1925065e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiQVBw2Rdf9psNzsTWGrB3aAaxho5v1MH-RD2F_zRYVzFLfY7vyqZFW2TFcp0XVFcdfyrW1pggDBu85mAfrs-J_uhdLWOtwAETu90UIGyahRiqiSzkgz9A8IMjc8TksmoD8vBtewcy0oId5/s1600/firewall-rule-03.PNG)  
*Налаштування WAN порту та VLAN*

[![](/assets/images/blog/ba3f0c1426d483e4-1cb0585ce1a61af9.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjiVdQRECLMx5K0hx_F5N0iogYx185yKVsdrXVufllrfV879Gx_99kic8jlYntVAqFIROs4QD1Au-TiocPwg2z8g63NZTkQuC9QTA8k3Of9nNAi2yGGyQ8j9_Ctcu7RqxGcq2HvuG9sKJj0/s1600/firewall-rule-04.PNG)  
*zone\_lan\_forward після додавання правила*

  
child.0.sh:   
/usr/sbin/iptables -D zone\_lan\_forward -j zone\_wan\_REJECT -s 192.168.0.128/28  
  
Цей запис видаляє, попередньо додані правила, і надає доступ до мережі інтернет.  
  
  
***cron***  
  
Для виконання команд за розкладом є прогама [cron](http://uk.wikipedia.org/wiki/Cron). Приклад налаштування: ["Scheduling Jobs With cron on OpenWrt"](http://martybugs.net/wireless/openwrt/cron.cgi) [en][[ua](http://translate.google.com.ua/translate?hl=uk&sl=auto&tl=uk&u=http%3A%2F%2Fmartybugs.net%2Fwireless%2Fopenwrt%2Fcron.cgi&anno=2)]  
Для того щоб вона виконувалася треба щоб був файл конфігурації що розміщується у теці /etc/crontabs  
  
У кожного користувача можуть бути свої файли налаштувань, створимо для користувача root  
  

```
touch /etc/crontabs/root
```

  
За допомогою редактора додамо записи до фала конфігурації */etc/crontabs/root*  
0 \*/2 \* \* \* /etc/fw-cron/child.1.sh > /dev/null  
30 \*/2 \* \* \* /etc/fw-cron/child.0.sh > /dev/null  
  
Правила виконуються кожну 2-гу годину.  
Наприклад, о 16-00 забороняємо доступ, о 16-30 дозволяємо доступ.  
  
Запускаємо   
*/etc/init.d/cron restart*  
  
  
***cutter***  
  
Якщо є бажання то можна і обмежити поточні з'єднання, тому що додані правила фаєрволу не розривають вже встановленні з'єднання. І програми що використовують постійне з'єднання продовжують працювати. Для розривання з'єднань встановимо пакунок [cutter](http://translate.google.com.ua/translate?hl=uk&sl=en&tl=uk&u=http%3A%2F%2Fwww.lowth.com%2Fcutter%2F&anno=2)  
opkg update   
opkg install cutter  
Є декілька варіантів з визначенням поточних з'єднань.  
1. Використаємо файл з IP адресами клієнтів наданих DHCP сервером роутера. І відфільтруємо тільки ті адреси що потрібно блокувати.  
  
while read inputline;  
do  
 ip4=$(echo "$inputline" | awk '{print $3;}')  
 ip44=$(echo $ip4 | cut -d "." -f 4)  
 if [[ $ip44 -ge 128 && $ip44 -le 148 ]]; then  
 /usr/sbin/cutter $ip4 >/dev/null  
 fi  
done < "/var/dhcp.leases"  
  
2. Використаємо файл зі значенням поточних з'єднань з використанням /proc/net/ip\_conntrack  
виконуючи таку послідовність  
grep ESTABLISHED /proc/net/ip\_conntrack | cut -d "=" -f 2| cut -d " " -f 1|sort -u   
отримаємо перелік адрес з поточним з'єднанням.   
Обробимо фільтруючи адреси:  
  
grep ESTABLISHED /proc/net/ip\_conntrack | cut -d "=" -f 2| cut -d " " -f 1|sort -u | while read ip4; do \  
 ip44=$(echo $ip4 | cut -d "." -f 4); \  
 if [[ $ip44 -ge 128 && $ip44 -le 148 ]]; then \  
 /usr/sbin/cutter $ip4 >/dev/null; \  
 fi \  
 done  
  
Додамо рядки з будь якої з версій реалізації до файлу /etc/fw-cron/child.1.sh. Таким чином у нас будуть блокуватися майбутні з'єднання і обриваються поточні.  
  
Перевірено на роутері TP-Link 1043, OpenWrt KAMIKAZE (bleeding edge, r25068) [18.10.2011]  
  
Якщо є потреба не обмежувати повний доступ,  а використовувати блокування на рівні доменного імені, то можна скористатися модулем [iptables-mod-filter](https://forum.openwrt.org/viewtopic.php?id=18776) - (iptables extensions for packet content inspection)   
  
opkg updare  
opkg info iptables-mod-filter  
  
Depends: iptables, kmod-ipt-filter  
  
  
opkg info   kmod-ipt-filter  
Depends: kernel (= 2.6.32.27-1), kmod-ipt-core, kmod-textsearch  
  
opkg info kmod-ipt-core  
Depends: kernel (= 2.6.32.27-1)  
  
Тому слід бути уважним щодо використовного вашого ядра операційної системи, у моєму випадку потрібні скомпільовані версії для  2.6.32.28-1, тому я їх створював за [цією процедурою](http://lexxai.blogspot.com/2011/10/dantes-openwrt-kernel-263228-tp-link-tl.html).  
  
  
opkg install iptables-mod-filter  
  
Для блокування тільки "vkontakte" та "odnoklassniki"  
/usr/sbin/iptables -I zone\_lan\_forward -j zone\_wan\_REJECT -s 192.168.0.128/28 -m string --string "vkontakte" --algo kmp  
/usr/sbin/iptables -I zone\_lan\_forward -j zone\_wan\_REJECT -s 192.168.0.128/28 -m string --string "odnoklassniki" --algo kmp  
  
 Або  блокування усього окрім "slovari.yandex.ru"  
/usr/sbin/iptables -I zone\_lan\_forward -j zone\_wan\_REJECT -s 192.168.0.128/28    
/usr/sbin/iptables -I zone\_lan\_forward -j zone\_wan\_ACCEPT -s 192.168.0.128/28 -m string --string "slovari.yandex.ru" --algo kmp
