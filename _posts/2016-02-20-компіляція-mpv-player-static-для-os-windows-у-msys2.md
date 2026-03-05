---
layout: post
title: "Компіляція mpv player (static) для OS Windows у MSYS2"
date: 2016-02-20 00:29:00 +0000
tags: ["ffmpeg", "mingw", "MPV", "msys", "msys2", "programming", "staic build"]
blogger_orig_link: https://lexxai.blogspot.com/2016/02/mpv-player-static-os-windows-msys2.html
---

На сьогодні спроба зробити компіляцію за методом "[Компіляція mpv player для OS Windows у VM OS OpenSUSE](http://www.lexxai.pp.ua/2015/12/mpv-player-os-windows-vm-os-opensuse.html)" має невдачу.  

[![](/assets/images/blog/6b96435d654bc798-96b4ed0439c4e6e0.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhw2BatqbWkeGqzWoMAcs25falG3yPQUNgLryWfJO9_vRRZJrqBCV9prhM-mhpCpynbDfREF8FA6Fox7WrNDcZ2k5yISsiyAQgv-qzMpXdE41DOQvfgoGYR5laf8kLCH-q5wv8g6XUD8YCJ/s1600/mpv-logo.PNG)  
*https://github.com/mpv-player*

Задача компілювати проект для операційної системи Windows x64. Тому використаємо метод "[Native compilation with MSYS2](https://github.com/mpv-player/mpv/blob/master/DOCS/compile-windows.md#native-compilation-with-msys2)" котрий рекомендовано на сайті [виробника](https://mpv.io/) .  
  
Якщо використовувати усе як описано, все працює для динамічного лінкування. Але якщо потрібно створити статичне лінкування без застосування додаткових DLL файлів, то виникає проблема у версії MPV "git-0d40140".  

```
./waf configure CC=gcc --enable-libmpv-shared --enable-static-build --enable-gpl3 --prefix=/mingw64
/waf install
...
[427/444] Compiling libmpv/mpv.def
[428/444] Compiling libmpv/mpv.pc.in
- install /mingw64/include/mpv/client.h (from libmpv/client.h)
- install /mingw64/include/mpv/qthelper.hpp (from libmpv/qthelper.hpp)
- install /mingw64/include/mpv/opengl_cb.h (from libmpv/opengl_cb.h)
- install /mingw64/share/applications/mpv.desktop (from etc/mpv.desktop)
- install /mingw64/etc/mpv/encoding-profiles.conf (from etc/encoding-profiles.conf)
- install /mingw64/share/icons/hicolor/16x16/apps/mpv.png (from etc/mpv-icon-8bit-16x16.png)
- install /mingw64/share/icons/hicolor/32x32/apps/mpv.png (from etc/mpv-icon-8bit-32x32.png)
- install /mingw64/share/icons/hicolor/64x64/apps/mpv.png (from etc/mpv-icon-8bit-64x64.png)
- install /mingw64/share/icons/hicolor/scalable/apps/mpv.svg (from etc/mpv-gradient.svg)
- install /mingw64/bin/mpv.com (from build/mpv.com)
[439/444] Linking build/mpv.exe
+ install /mingw64/bin/pkgconfig/mpv.pc (from build/libmpv/mpv.pc)
[441/444] Linking build/mpv-1.dll
D:/develop-tools/msys64/mingw64/lib\libcelt0.a(celt.o):(.text+0x4d50): multiple definition of `celt_encoder_get_size'
D:/develop-tools/msys64/mingw64/lib\libopus.a(celt_encoder.o):(.text+0x270): first defined here
D:/develop-tools/msys64/mingw64/lib\libcelt0.a(celt.o):(.text+0x4ef0): multiple definition of `celt_encoder_init'
D:/develop-tools/msys64/mingw64/lib\libopus.a(celt_encoder.o):(.text+0x5f60):
...
```

  
За рекомендаціями суспільства [rossy](https://github.com/rossy) [igv](https://github.com/igv), знайдено - що проблемою є використання пакунку [ffmpeg](https://www.ffmpeg.org/), та інших що встановлюється за командами:   

```
pacman -S mingw-w64-x86_64-ffmpeg
pacman -S mingw-w64-x86_64-libdvdnav mingw-w64-x86_64-libguess
```

Тому є рекомендація видалити ці пакунки , і скомпілювати [ffmpeg](https://www.ffmpeg.org/) самостійно з динамічними бібліотеками.  

```
pacman -R mingw-w64-x86_64-ffmpeg
pacman -R
```

mingw-w64-x86\_64-libbluray
pacman -R mingw-w64-x86\_64-libguess
rm \mingw64\x86\_64-w64-mingw32\lib\libpthread.dll.a
rm \mingw64\lib\libjpeg.dll.a
rm \mingw64\lib\libiconv.dll.a
Наступний крок компіляція  ffmpeg. Є [інструкція для компіляції ffmpeg для MSys](https://trac.ffmpeg.org/wiki/CompilationGuide/MinGW).  

```
cd ~
git clone https://github.com/FFmpeg/FFmpeg
cd ffmpeg
./configure --enable-gpl --disable-debug --prefix=/mingw64 --enable-shared 
make -j4 && make install
```

Якщо в системі не має пакунку make, треба встановити його.  

```
pacman -S make
```

Після цього можна приступати до компіляції MPV.  

```
./waf configure CC=gcc --enable-libmpv-shared --enable-static-build --enable-gpl3 --prefix=/mingw64   
./waf install
```

[![](/assets/images/blog/7a2d0ab28f1e23cd-4c9360bf87af385f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiXFbwgERXP8n28Znq08mm87mdfUEicqKsA5rVaujQd5PrQDM8dcECKCvDC1luIE4nZP_93zBdTTdAklhQQSdGBl7qjCoZtWWhs_Li4OxYvdO4VqwQ68_6sjUeR_iQ476NQeDhOzi2IM_oZ/s1600/mpv-msys2.PNG)  
*mpv компіляція*

У осатаній спробі (2019-05) зробити статичну велику бібліотеку mpv-1.dll була не вдалою, тому використав тільки  --enable-libmpv-shared і  зібрав усі залежні бібліотеки за допомогою скрипту.  
  
Додаю скрипт (mpv-build/scripts/mpv-searchlib) котрий збирає динамічно залежні бібліотеки до однієї теки:  

```
#!/bin/sh
set -e

BUILD="$(pwd)/.."
BUILDML=$BUILD/build_libs/mpvlibs
BUILDMPV=$BUILD/mpv/build

mkdir -p $BUILDML

cp $BUILDMPV/mpv-1.dll $BUILDML

export PATH="$BUILD/build_libs/bin/:$PATH"

for i in $(ldd $BUILDML/mpv-1.dll | grep -v "/c/" | awk '{print $3}' | grep / )
do
 echo $i
 cp  $i $BUILDML
done
```

  
P.S.   
А якщо не виходить то може краще взяти готовий білд тут : <https://mpv.srsfckn.biz/> ?
