---
layout: post
title: "Викорисатння ImageMagic у debug режимі. Qt5, C++."
date: 2016-01-15 22:57:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2016/01/imagemagic-debug.html
---

[![](/assets/images/blog/55a05ad40f9c7e2c-d12f266c764f9209.jpg)](http://www.imagemagick.org/image/wizard.jpg)

Роблю собі нотатку.  
Стала потреба тестувати С++ проект з середовища Qt5, де використовується [API](http://www.imagemagick.org/script/magick++.php) від проекту [ImageMagic](http://www.imagemagick.org/).  
  
Якщо використати у режимі (debug)  Qt5 проект, то отримаємо помилки на кшталт цих:  
*Exception at 0x7ff9a2051f08, code: 0xe06d7363: C++ exception, flags=0x1*   
*(execution cannot be continued) (first chance) in CORE\_RL\_Magick\_\_\_!Magick::throwException "createIM Magick: unable to open module file `C:\\Users\\user\\.magick\\IM\_MOD\_RL\_?\u0002?F?\u0001\_.dll': No such file or directory @ warning/module.c/GetMagickModulePath/812"*  
  
Для вирішення проблеми я скомпілював [ImageMagic](http://www.imagemagick.org/). За [інструкцією](http://www.imagemagick.org/script/install-source.php#windows) отримав VisualMagick проект для Visual Studio, там вибрав конфігурацію Debug.  

[![](/assets/images/blog/c20eecd5d07ef590-a5a7fe7d06bcda4e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiIhyphenhyphenWaTGgMw7uShfcMXuz7LVoknk4SMLltHsORi_IU6riSSGPfaIQqe9TabrXqUOhaTpYS8NpAFPmwjCBwmNNuzviMBCcPZxgJF5dmkC0jAo2dGW8vZbaSrnvIoAcIAmb9x_A-0UvDdjUz/s1600/im-debug.PNG)  
*Конфігурація Debug*

Перекомпілював проект VisualDynamicMT  
Отримав за шляхами:  
libraries : *ImageMagick-src-windows\ImageMagick-6.9.3-0\VisualMagick\lib*  
DLL: *ImageMagick-src-windows\ImageMagick-6.9.3-0\VisualMagick\bin*  
  
ImageMagik DLL для Release мають вигляд **CORE\_RL**, для Debug - **CORE\_DB**  
Відредагував файл проекту Qt project file .pro:   

`#ImageMagick Src 6.9.3-Q16  
  
win32:CONFIG(release,
debug|release): LIBS +=
-LD:\develop-tools\ImageMagick\ImageMagick-src-windows\ImageMagick-6.9.3-0\VisualMagick\lib
-lCORE_RL_Magick++_ -lCORE_RL_magick_ -LCORE_RL_wand_  
else:win32:CONFIG(debug,
debug|release): LIBS +=
-LD:\develop-tools\ImageMagick\ImageMagick-src-windows\ImageMagick-6.9.3-0\VisualMagick\lib
-lCORE_DB_Magick++_ -lCORE_DB_magick_ -LCORE_DB_wand_`

  
Додатково. Для того щоб ImageMagik не шукав шляхи розміщення DLL у windows registry, а у теці проекту додав у  
*ImageMagick-src-windows\ImageMagick-6.9.3-0\ImageMagick\magick\magick-config.h*:  

`#ifndef MAGICKCORE_INSTALLED_SUPPORT  
#define MAGICKCORE_INSTALLED_SUPPORT   
#endif`
