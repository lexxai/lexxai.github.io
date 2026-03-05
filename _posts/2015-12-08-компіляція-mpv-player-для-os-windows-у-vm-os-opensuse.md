---
layout: post
title: "Компіляція mpv player для OS Windows у VM OS OpenSUSE"
date: 2015-12-08 20:32:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2015/12/mpv-player-os-windows-vm-os-opensuse.html
---

[![](/assets/images/blog/2691d04b72074be9-96b4ed0439c4e6e0.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhkQtW4cI1eXXkOCy1ZQa4Xy_K6VNqRaSQB7MPyN0U0TIJtscLlO59Q-2lIcelmugLDV-bEW6G8zVPMdZNwmMoJuwwmjezMNJOThwS73sBsWnncCZM5w2Ph8woS1dxQF_-DKC_8Gnyqz6dc/s1600/mpv-logo.PNG)  
*https://github.com/mpv-player*

Задача компілювати проект для операційної системи Windows x64, у VM linux OpenSUSE.  
Є готове середовище для компіляції у вигляді образу для віртуальної машини.  
  
Download from <http://srsfckn.biz/mingw-images/> file MinGW-w64-env.x86\_64-0.2.0.vmdk.xz (2015-Jun-16 17:55)  
  
  
  1. extract MinGW-w64-env.x86\_64-0.2.0.vmdk.xz  
  2. Create new VM VMware. Linux (OpenSUSE 64-bit), 3072MB of RAM, 4 - CPU, DVD, Network Adapter, Display.  
  
УВАГА: Пам'яті потрібно не менше ніж 2 ГБ.   
  
  3. Add hard Disk, SCSI,  use an existing virtual disk, file name : MinGW-w64-env.x86\_64-0.2.0.vmdk, convert to new format.  
  4. Play VM.  
  5. Boot as default.  

[![](/assets/images/blog/7281148b574c8433-935fc8d990218ec9.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhdmDruBVLOpvAO6H1z4pxWtCXghYkIqiHUfpaVeAY6ycOhGeLJIz7rbAgziup_Q8tMx-Adihe-1I2NM-Z-XZXMeNSTeaS5ov5TwR8h5KCll-FWIfNLGp_PoZiLQgMHxWh0yFQ9f3DJSUQt/s1600/boot.PNG)  
*boot VM*

  
  6. Check IP, and connect to VM via SSH client. password: build.  
  7. git clone https://github.com/lachs0r/mingw-w64-cmake  
  8. cd mingw-w64-cmake  
  

[![](/assets/images/blog/17ee4a88bfb1bd1e-8b67bf4caae7eeb8.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgv_-GNrS4QA5TVM0JbxfYlTOI7-XUgPS_WLPbrC4aLDMu-g3cty20ZPRcUdpa66hipVtKFxCQ2lTtYLTKKgdAoXu3IpFNXb5MzgnqilO0S4gmAUfn2IEC588q8KeA_kNa0i8VejT-tDO_-/s1600/git-lachs0r-mingw-w64.PNG)  
*git clone*

  9. cat README.rst  
  10. mkdir build-64  
  11. cd build-64  
  12. cmake -DTARGET\_ARCH=x86\_64-w64-mingw32 -DCMAKE\_INSTALL\_PREFIX=prefix -G Ninja ..  

[![](/assets/images/blog/5962696e324502de-70d5d397d83d97c4.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiUFpq7coh0B3VX_HxT57WPWwwjyfj5dLTv6n-eDR8uazGSUdO8spbuJptpB2wIhE_3W88uplGPxcxGzP5QsIsWLLMCoVW5HCf2uGeU0SzHi_Y4hqdcREiurpCTjoP2Bcisd20NNTJv9fH5/s1600/cmake-DTARGET.PNG)  
*cmake*

  
  13. ninja mpv  
  
У процесі компіляції, виявлено що процес переривається  

```
[232/250] Performing configure step for 'ffmpeg'
FAILED: cd /root/mingw-w64-cmake/build-64/packages/ffmpeg-prefix/src/ffmpeg-build && /usr/bin/cmake -P /root/mingw-w64-cmake/build-64/packages/ffmpeg-prefix/src/ffmpeg-stamp/ffmpeg-configure.cmake && /usr/bin/cmake -E touch /root/mingw-w64-cmake/build-64/packages/ffmpeg-prefix/src/ffmpeg-stamp/ffmpeg-configure
CMake Error at /root/mingw-w64-cmake/build-64/packages/ffmpeg-prefix/src/ffmpeg-stamp/ffmpeg-configure.cmake:16 (message):
  Command failed: 1

   '/root/mingw-w64-cmake/build-64/exec' '/root/mingw-w64-cmake/build-64/packages/ffmpeg-prefix/src/ffmpeg/configure' '--cross-prefix=x86_64-w64-mingw32-' '--prefix=/root/mingw-w64-cmake/build-64/prefix/mingw' '--arch=x86_64' '--target-os=mingw32' '--target-exec=wine' '--pkg-config-flags=--static' '--enable-cross-compile' '--enable-runtime-cpudetect' '--enable-gpl' '--enable-version3' '--enable-nonfree' '--enable-avresample' '--enable-postproc' '--enable-avisynth' '--enable-libass' '--enable-libbluray' '--enable-libdcadec' '--enable-libfdk-aac' '--enable-libgme' '--enable-libmodplug' '--enable-libmp3lame' '--enable-libopencore-amrnb' '--enable-libopencore-amrwb' '--enable-libopus' '--enable-libspeex' '--enable-libtheora' '--enable-libvorbis' '--enable-libvpx' '--enable-libx264' '--enable-libxvid' '--enable-openssl'

  See also

    /root/mingw-w64-cmake/build-64/packages/ffmpeg-prefix/src/ffmpeg-stamp/ffmpeg-configure-*.log


ninja: build stopped: subcommand failed.
linux:~/mingw-w64-cmake/build-64 #
```

Після перегляду лог файлу з'ясовано що не знайдено openssl.  
Інсталюємо пакунок до системи:   

```
zypper install openssl
```

  
Після цього знову компілюємо:  

```
ninja mpv
```

У разі успіху, маємо  

```
Performing build step for 'mpv'
-- mpv build command succeeded.  See also /root/mingw-w64-cmake/build-64/packages/mpv-prefix/src/mpv-stamp/mpv-build-*.log
Completed 'mpv'
```

  
  
Результат тут: mpv.exe, mpv.com    

```
~/mingw-w64-cmake/build-64/packages/mpv-package
```

Результат статичної бібліотеки тут:     

```
~/mingw-w64-cmake/build-64/packages/mpv-dev/mpv-1.dll
```

```
libmpv.dll.a  aka  mpv.lib
```

Якщо треба змінити опції компіляції, наприклад додати --enable-gpl3 для права використання сторонніх модулів, то правимо файл.  

```
~/mingw-w64-cmake/packages/mpv.cmake
```

Після цього знову компілюємо:
  

```
ninja mpv
```

P.S. Для редагування використовую пакунок  mc :
  

```
zypper install mc
```

```
 
```

  
Інший метод компіляції:  

```
Компіляція mpv player (static) для OS Windows у MSYS2
```
