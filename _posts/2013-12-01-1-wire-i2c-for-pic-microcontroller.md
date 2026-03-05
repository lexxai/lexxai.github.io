---
layout: post
title: "1-wire, I2C for PIC Microcontroller"
date: 2013-12-01 23:45:00 +0000
tags: ["hardware", "microchip", "pic"]
blogger_orig_link: https://lexxai.blogspot.com/2013/12/1-wire-for-pic-microcontroller.html
---

[![](/assets/images/blog/be490318d78bdaf2-fd68330f5d4d4be3.gif)](http://wiki.linuxmce.org/images/3/36/DS18S20.gif)

  
[1-Wire - From Wikipedia, the free encyclopedia](http://en.wikipedia.org/wiki/1-Wire)   
  
1
-Wire (англ. один провід) - двонаправлена ​​шина зв'язку для пристроїв з низькою швидкістю передачі даних (зазвичай 15,4 Кбіт/с , максимум 125
Кбіт/с), в якій дані та живлення передаються по одній лінії (тобто
всього використовуються два дроти - один для заземлення , а другий для живлення і даних ; в деяких випадках використовують і окремий провід живлення).  
  
 Відповідно, топологія такої мережі - загальна шина. Мережа пристроїв 1 -Wire зі зв'язаним основним пристроєм названа « MicroLan ».Одна з привабливих характеристик шини - той факт, що для зв'язку з пристроєм необхідно лише два дроти: для даних і для заземлення. Інша
приваблива характеристика шини - велика відстань передачі.  
  
[AN1199 - 1-Wire Communication with PIC Microcontroller](http://www.microchip.com/stellent/idcplg?IdcService=SS_GET_PAGE&nodeId=1824&appnote=en535817)   
[1-wire slave emulator for PIC16 microcontroller – owslave](http://www.fabiszewski.net/1-wire-slave/)  
 [1-Wire Humidity sensor + I/O-module](http://home.kpn.nl/thomas_7/1Wire/1-WireIOPort.html)  
[1-Wire. Измерение температуры. Термодатчик DS18B20.](http://picdevices.ru/eksperimentyi/1-wire-izmerenie-temperaturyi-termodatchik-ds18b20.html)  
[Что такое 1‑Wire?](http://www.elin.ru/1-Wire/)  
  
I*2*C  
  
[AN541 - Using PIC16C5x as a Smart IIC Peripheral (emulating full I2C specification )](http://www.microchip.com/stellent/idcplg?IdcService=SS_GET_PAGE&nodeId=1824&appnote=en011023)  

[I2C Master/Slave 16F88/16F767 working code](http://www.picbasic.co.uk/forum/showthread.php?t=5632&s=a4ac54ad6cdeb9ead3ea5e2653e89978)

[Video : PIC10F222 8bit ADC I2C LCD test](http://youtu.be/wbcwaOOkrHo)  
[Video : PIC10F220 + ADT7410 Temperature Sensor + I2C LCD AQM0802A](http://youtu.be/LPNvDUP59ag)  
З відео код:
  

```
:020000040000FA
:1000000025000504F80C0600C00C0200BD09380­CE0
:100010009409390C9409140C9409700C9409560­C29
:1000200094096C0C9409380C94090C0C9409010­C7B
:1000300094098709D409BD09800C94098709D40­95A
:10004000070C3400C30914022A099409F402220­A95
:100050008709330AE2010008300831083408370­8FC
:100060005408440841087809900C9409010C940­93B
:100070007809910C9409A70917023D00900C940­986
:10008000000C94097809910C9409A70917023C0­007
:1000900087097D037C030307520AFF0CBC012D0­C6A
:1000A0003B00540A200C3B00790078000A0CBC0­08D
:1000B000B9020306560AF9000A0CDC013800BD0­932
:1000C000010C94098709BD09C00C94098709D40­95A
:1000D000C3091B0294091902C90994091802C90­924
:1000E0009409DF0C9409430C940987090000330­A32
:1000F000060C26001E0C35000000F5027C0A460­4A2
:100100001E0C35000000F502820A26040008260­5B0
:100110001E0C35000000F5028A0A46051E0C350­04B
:100120000000F502900A00083600080C3300760­340
:1001300003069E0A460426052604A10A4605260­54E
:100140002604F302970A4604260526040008FC0­C40
:100150000600080C330026054606B20A0304770­39E
:100160002604B50A030577032604F302AB0A460­406
:1001700026052604F80C0600000878097C0C940­972
:10018000000C9409000878097C0C9409400C940­92F
:100190000008E20130083108320833083408350­815
:1001A0003608370838083908FF0C3000140C310­0C5
:0C01B0000000F102D80AF002D60A000894
:0401BC00000800082F
:021FFE00EB0FE7
:00000001FF
```

...
  
  
  
[I2C - Матеріал з Вікіпедії — вільної енциклопедії.](http://uk.wikipedia.org/wiki/I2C)  
[A single master I2C tutorial.](http://www.best-microcontroller-projects.com/i2c-tutorial.html)  
[Testing Pic code for I2C Master/Slave communication](http://botdream.com/oldsite/blog/2008/02/01/testing-pic-code-for-i2c-masterslave-communication-2/index.html)  
[Lab 14: Inter-Integrated Circuit (I2C) communication](http://embedded-lab.com/blog/?p=2583)
