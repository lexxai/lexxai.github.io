---
layout: post
title: "FreeNAS  + Jail : VirtualBox : Microsoft Hyper-V 2016"
date: 2016-11-09 22:06:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2016/11/freenas-jail-virtualbox-microsoft-hyper.html
---

Зробив експеримент на базі [FreeNAS](https://uk.wikipedia.org/wiki/FreeNAS)-9.10.1-U3 (d985cd1) - [гіпервізор](https://uk.wikipedia.org/wiki/%D0%93%D1%96%D0%BF%D0%B5%D1%80%D0%B2%D1%96%D0%B7%D0%BE%D1%80) в гіпервізорі та ще і в [jail](https://en.wikipedia.org/wiki/FreeBSD_jail).  
  
Використано:  

* CPU - Intel(R) Core(TM) i5 CPU 650 @ 3.20GH
* [FreeNAS-9.10.1-U3 (d985cd1)](https://www.blogger.com/)
* Гіпервізор у [FreeNAS](https://wiki.freenas.org/index.php/Main_Page) - VirtualBox-4.3.12
* Гіпервізор у [VirtualBox](https://www.virtualbox.org/) - [Microsoft Hyper-V 2016](https://technet.microsoft.com/en-us/hyper-v-server-docs/hyper-v-server-2016) ( VT-x/AMD-V)

  
  

[![](/assets/images/blog/e8846644b0d4a069-edb6f93c029f55dc.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiHpNu-d1wXCrtJvX_VLxx3oHXmbW3Cf6cT-PUUYBODED8TyAatXuHxvXXiPOUHTAB_09nu_-nJ45kvoxsU61yeXnxcQCmxZK4OHPJTcyMCvMZCbZ4yBJSnpUrwT6o0lE_SHvCRByNTDswh/s1600/phpVirtualBox+-+VirtualBox+Web+Console+-+Mozilla+Firefox_008.png)  
*phpVirtualBox для створення віртуальної машини*

  
  

[![](/assets/images/blog/fc65a59b4e3528a6-44bf7efeea8ddf02.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj6DwOFDQoKxL3f63qlCZOSn47kYmkBAiTAlgUu2XylK9Qap02DCB2Wqgb_45U0-KKhR05RdZVGa58EDj1mOt8my4mawiPWMOStUxJA9rf9N9L4ueVsZAgHZp1otrqmgj6CqMLP9Y6uJKvw/s1600/HV_3389_009.png)  
*Підключення до Core Microsoft Hyper-V 2016*

  
Надалі треба створити вкладену віртуальну машину...
