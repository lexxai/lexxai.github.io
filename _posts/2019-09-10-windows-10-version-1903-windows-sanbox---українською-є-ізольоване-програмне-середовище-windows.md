---
layout: post
title: "Windows 10, version 1903, Windows Sanbox - Українською є \"Ізольоване програмне середовище Windows\"."
date: 2019-09-10 19:11:00 +0000
tags: ["Sandbox", "Virtualization", "windows 10"]
blogger_orig_link: https://lexxai.blogspot.com/2019/09/windows-10-version-1903-sanbox.html
---

Маючи Windows 10 Pro, і оновившись до version 1903 [прочитав новини](https://cybercalm.org/novyny/yak-vykorystovuvaty-windows-sandbox-v-ostannomu-onovlenni-windows-10-instruktsiya/) : що є нова можливість використовувати вбудовану "[Sanbox](https://uk.wikipedia.org/wiki/%D0%9F%D1%96%D1%81%D0%BE%D1%87%D0%BD%D0%B8%D1%86%D1%8F_(%D0%BA%D0%BE%D0%BC%D0%BF%27%D1%8E%D1%82%D0%B5%D1%80%D0%BD%D0%B0_%D0%B1%D0%B5%D0%B7%D0%BF%D0%B5%D0%BA%D0%B0))".  
Але сходу не зміг ввімкнути цю можливість користуючись англомовними ресурсами.  
Деякі [ресурси написали](https://phoneinfo8.info/windows-sandbox/) що в українській версії не запускався Sandbox у свій час.  
То вирішив перевірити і сам. Так я знайшов що в українській версії Windows 10 Pro (1903 збірка 18362.295) Sandbox засукається, а засіб "Windows Sandbox" у перекладі є "Ізольоване програмне середовище Windows".  

[![](/assets/images/blog/403e580d3464f1c8-6786294159ad0e13.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjArJirktVqQRU5OxoPbu9164XWXkK5HLrtDrIAhwAzeujz9rrUb_dkdcf7ypeUADTocaM9ARPAlC920oNs4UaLqMDUxclxSXrnLWftj6bJUsFW0b2CEkbo8pI3A3-DNgOv-iZSQpYcQdr9/s1600/SandboxieInstall_Ukr.PNG)  
*"Ізольоване програмне середовище Windows" - Windows Sandbox*

Всередині тимчасової віртуальної машини запускається не активована Windows 10 Enterprise 1903 з англомовним інтересом.  
  
Додатково можна трохи налаштувати "[Windows Sandbox](https://techcommunity.microsoft.com/t5/Windows-Kernel-Internals/Windows-Sandbox-Config-Files/ba-p/354902?WT.mc_id=thomasmaurer-blog-thmaure)" через створений текстовий  конфігураційний файл .wsb. Наприклад:  
  

```
<Configuration>
<MappedFolders>
   <MappedFolder>
     <HostFolder>C:\temp</HostFolder>
     <ReadOnly>true</ReadOnly>
   </MappedFolder>
   <MappedFolder>
     <HostFolder>C:\CodingProjects</HostFolder>
     <ReadOnly>false</ReadOnly>
   </MappedFolder>
</MappedFolders>
<LogonCommand>
   <Command>C:\users\wdagutilityaccount\desktop\CodingProjects\VSCodeInstall.cmd</Command>
</LogonCommand>
<VGpu>default</VGpu> 
<Networking>default</Networking> 
</Configuration>
```

  
Для запуску "Windows Sandbox" з урахуванням  конфігураційного файлу потрібно подвійним кліком натиснути на конфігураційний файл у провіднику.  
  
Посилання на спільні теки ("temp","CodingProjects") будуть на робочому столі, і автоматично запуститься скрипт "CodingProjects\VSCodeInstall.cmd", а тека "temp" буде тільки для читання.  
  
#Windows10Sandbox #Windows10
