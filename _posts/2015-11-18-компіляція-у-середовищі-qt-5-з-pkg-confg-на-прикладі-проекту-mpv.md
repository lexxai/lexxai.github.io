---
layout: post
title: "Компіляція у середовищі Qt 5 з pkg-confg на прикладі проекту MPV"
date: 2015-11-18 17:33:00 +0000
tags: ["C++", "MPV", "pkg-confg", "programming", "Qt 5", "windows", "програмування"]
blogger_orig_link: https://lexxai.blogspot.com/2015/11/qt-5-pkg-confg-mpv.html
---

[![](/assets/images/blog/46cca55a7317a208-934816aa5347b846.png)](https://upload.wikimedia.org/wikipedia/uk/thumb/8/88/Qt_logostrap_CMYK.png/220px-Qt_logostrap_CMYK.png)

Для компіляції проекта "[MPV](https://github.com/mpv-player/mpv)" у середовищі "[Qt5](http://www.qt.io/)" використовується наступна команда PKGCONFIG з прикладу "mpv\DOCS\client\_api\_examples\qt\qtexample.pro"  
  
PKGCONFIG += mpv  
  
Для компіляції потрібно мати  pkg-config.exe, можна використати з  пакунку msys2, котрий потрібен для компіляції MPV під Windows.  ...\msys64\usr\bin\pkg-config.exe  
Потрібно додати до змінної оточення PATH шлях: з msys64\usr\bin\  
  
Далі потрібен файл "mpv.mc".  
Я створив у теці проекту, під теку pkgconfig і розмістив там "mpv.mc" (після компіляції він є за адресою msys64\home\username\mpv\build\libmpv).  
  
Для того щоб "Qt5" шукав ". mc" файл у теці "pkgconfig" потрібно створити змінну оточення "PKG\_CONFIG\_PATH=pkgconfig".  Або додати цю змінну у властивостях Qt5 проекту "Build Enviroment".  
  
mpv.mc   

```
prefix=D:/develop-tools/msys64/mingw64
exec_prefix=D:/develop-tools/msys64/mingw64
libdir=D:/develop-tools/msys64/mingw64/lib
includedir=D:/develop-tools/msys64/mingw64/include

Name: mpv
Description: mpv media player client library
Version: 1.20.0
Requires:
Libs: -L${libdir} -lmpv
Cflags: -I${includedir}
```

  
D:/develop-tools/msys64/mingw64/lib/mpv.lib
