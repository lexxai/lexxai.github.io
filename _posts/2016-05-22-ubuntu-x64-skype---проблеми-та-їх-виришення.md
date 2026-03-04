---
layout: post
title: "Ubuntu x64, skype - проблеми та їх виришення"
date: 2016-05-22 23:19:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2016/05/ubuntu-x64-skype.html
---

Переходжу на використання Ubuntu як основну операційну систему.   
І у моєму випадку не працювала зв’язка Ubuntu робоча станція версії 14.04, 64 розрядна і skype 4.3.0.37, і WebCam TRUST WB 3320X.  

[![](/assets/images/blog/313c61302012789b-8b6b9f7c410d8029.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiz93xLm4Ed_iDulu3zIfm0Yzt4giicB4cuCN_VBvWAQZ5_gxFNpsBB9TFp5wHqq0Eb_3OTG-cWctE1MsGTutzK8ETj0iEJp793wBNHRF1fSCVW5pfUUQVL41rQibxUd6G10J28T5xjJOK1/s1600/skype-ver.png)  
*Linux Skype 4.3.0.37*

[![](/assets/images/blog/030d80885eae3384-c678868debbf5a31.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjWrUk2PSfv3l-yxQ-vsrZSw5MPhEzH6I5j1_cbkB_pCItvcWkyIh7utktPOgPe9LfxrjT236w-i5_hdDiJ1rHfUxws3VFbp27LrYxW5lk-FlZXhfSPQNMJfoCrldoJEiGTmW-WK5Z2RB0R/s1600/skype-novideo.png)  
*Не працює відео*

  
  
Використовуючи знання з публікацій:  

* [How to Install and configure webcam trust WB 3320X Live on Ubuntu /Debian Linu](http://www.pc-freak.net/blog/install-configure-webcam-trust-wb-3320x-live-ubuntu-debian-linux)
* [14.04 - webcam flipped aka v4l1compat.so troubles - Ask Ubuntu](http://askubuntu.com/questions/462547/webcam-flipped-aka-v4l1compat-so-troubles)

З’ясував що головна проблема, це використовувати сумісний режим доступу до підсистеми відео для програми skype.  
Для цього використовується трюк з LD\_PRELOAD та використання бібліотеки v4l1compat.so перед запуском прогами.   
Але якщо використовувати  v4l1compat.so що інстальовані у системі за шляхом /usr/lib/x86\_64-linux-gnu/libv4l/v4l1compat.so то отримаю повідомлення:  

```
bash -c 'LD_PRELOAD=/usr/lib/x86_64-linux-gnu/libv4l/v4l1compat.so skype'
ERROR: ld.so: object '/usr/lib/x86_64-linux-gnu/libv4l/v4l1compat.so' from LD_PRELOAD 
cannot be preloaded (wrong ELF class: ELFCLASS64): ignored.
```

З цього можна зробити висновок що треба використовувати 32 розрядну версію. Завантажити специфічну i386 версію можна так:  

```
sudo apt-get install -y libv4l-0:i386
```

І тепер запустити skype через команду:  

```
bash -c 'LD_PRELOAD=/usr/lib/i386-linux-gnu/libv4l/v4l1compat.so skype'
```

[![](/assets/images/blog/4fe066e9d54b81d9-9ff979e8c633a292.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh_iL35_aIjmpDcmeQr62tlp8c6j9ocOFUaP3CJBYIHH9L3VZBavqy62Hcpe7uIRYOIPEtcs8SoU1Wy7zcHqqZjfOu0eZPgIqeEP0QwQnCF3ghCAThDC0qgWQ0ZW5Pu1fCTomT-Blco85C9/s1600/skype-cam.png)  
*Тепер відео працює*

Для запуску програми я змінив ярлик запуску програми skype - /usr/share/applications/skype.desktop  
де замінив рядок запуску з   

```
Exec=env PULSE_LATENCY_MSEC=60 skype %U
```

на  

```
Exec=env LD_PRELOAD=/usr/lib/i386-linux-gnu/libv4l/v4l2convert.so  PULSE_LATENCY_MSEC=60 skype %U
```

[![](/assets/images/blog/2c014a31c5a14a60-ec52b79ccb537e3f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgzZbujQ1Z5PtX11UtEqe2r31KKeOyubON7VuE4Wkwjb9XrqRlkVU4tHOh5_MB1oqc2wG8USOdUOlDrhMOCYnqmRIJ213biLQ8QmhHZe8mxo6POBN3dZ0NddwMW1Cc9Qlx63I9xQqhnasDC/s1600/skype-app.png)  
*Модифікований ярлик запуску Skype у Ubuntu Linux*

  
Якщо у Вас не має доступу на зміни у системній теці то можна скопіювати файл skype.desktop до себе на робочий стіл і модифікувати його.  
  
Наступні проблеми:  

* Іконку Skype не видно в трею у Ubuntu 16.04, для виправлення цієї проблеми потрібно встановити ***sudo apt-get install sni-qt:i386*** ([за матеріалами askubuntu.com](http://askubuntu.com/questions/286233/how-to-add-a-skype-indicator))
* Часом шрифт в Skype стає італік. ALT-F2, qtconfig, стиль Cleanlooks, підправити шрифт а Regular. ([За матеріалами](https://community.skype.com/t5/Linux-archive/Skype-4-2-0-11-on-Ubuntu-italic-bold-font/td-p/1655883))
