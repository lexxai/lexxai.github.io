---
layout: post
title: "Компіляція браузера Chromium для Ubuntu (x265, HEVC)"
date: 2016-09-26 21:05:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2016/09/chromium-ubuntu-x265-hevc.html
---

[![](/assets/images/blog/0af759d67d479d2d-c3f111db30997962.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjJbHylzJVaLJDpqTdh6wJuXB5cVM1feVXRnv6fbQUH8lJWBvVGUIe63W4hRpLCHAkyel0FxvphTS52eUwbe2zEx5V2E3nqo-7gLdYxQ5ZI_OmSoeBdwBgFU-fM8EKCPPMAYzNXfUnmp4rv/s1600/x265.png)  
Є перевірена новина, що у експериментальній версії браузера [Chromium](https://uk.wikipedia.org/wiki/Chromium) [55.0.2850 для Windows](https://github.com/henrypp/chromium/releases/download/v55.0.2850.0-r416466-win64/chromium-sync.zip) з'явилася можливість програвати медіа файли формату ([HEVC або він же h265](https://uk.wikipedia.org/wiki/H.265)).  
Але стала необхідність скомпілювати браузера [Chromium](https://uk.wikipedia.org/wiki/Chromium) для операційної системи linux у моєму випадку це Ubuntu 16.04 з можливістю відтворювати формату ([HEVC або він же h265](https://uk.wikipedia.org/wiki/H.265)).  
  
[![](/assets/images/blog/b725db50311aab94-c9f4aba4e27393bb.gif)](https://www.chromium.org/_/rsrc/1438879449147/config/customLogo.gif?revision=3)Офіційна інструкція - [Get the Code: Checkout, Build, & Run Chromium](http://dev.chromium.org/developers/how-tos/get-the-code) ([linux](https://chromium.googlesource.com/chromium/src/+/master/docs/linux_build_instructions.md))  

1. Створюємо робочу теку:

   ```
   $ mkdir chromium
   $ cd chromium
   ```
2. Завантажуємо собі клон репозиторії  [depot\_tools package](https://commondatastorage.googleapis.com/chrome-infra-docs/flat/depot_tools/docs/html/depot_tools_tutorial.html#_setting_up).  

   ```
   $ git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git
   ```
3. Додаємо теку до змінної оточення, додаючи до файлу ~/.bashrc рядок:  

   ```
   export PATH=$PATH:/path/to/depot_tools
   ```

   Пере відкриваємо консоль термінала, і змінна повинна бути завантажена до оточення.
4. Скрипт для завантаження початкового коду Chromium: \  

   ```
   $ fetch chromium
   ```

   [![](/assets/images/blog/2ce7087ec6c07988-b778667a05cfbde0.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhA9hR2l7HmLxpwquM6ENHjWwLS79LuSVtSZwkiGExjkqeptFiTPByjx_NJYaINnRzSLzELtED9my97j9dufE2ebuGFscFyVBLXuQF-7S9fSn8UAYQnx0CEy0vef8aSpFZecZ67LsNZuDVY/s1600/fetch_chromium.png)  
   *fetch chromium*

   Скрипт буде довго працювати в залежності від швидкості Вашого з'єднання з мережею Iнтернет. Після закінчення роботи у Вас буде тека src, і конфігураційний файл .gclient
5. При першій компіляції необхідно завантажити залежності від інших програм:  

   ```
   $ cd src
   ```

   Надалі усі команди виконуються у теці src.

   ```
   $ ./build/install-build-deps.sh
   ```

   І по закінченню синхронізувати початковий код  у теці src.  

   ```
   $ gclient runhooks
   ```
6. Бажано мати або отримати ключі розробника Google :  [install API keys](http://dev.chromium.org/developers/how-tos/api-keys)
7. Компілювати будемо до теки  out/Default за допомогою мета системи [GN](https://chromium.googlesource.com/chromium/src/tools/gn/). Вона використовує файл налаштувань BUILD.gn, і генерує файли для системи компіляції Ninja. [Визначмо агрументи для створення робочої теки та файлу BUILD.gn](https://chromium.googlesource.com/chromium/src/+/master/tools/gn/docs/quick_start.md).  

   ```
   $ gn args out/Default
   ```

   Відкриється Ваш системний текстовий редактор і потрібно додати наступні значення для Google API keys, та опції модулів компіляції:

   ```
   google_api_key = "XXXXXXXXXXXXXXXXXXX"
   google_default_client_id = "YYYYYYYYYYY.apps.googleusercontent.com"
   google_default_client_secret = "ZZZZZZZZZZZZ"
   proprietary_codecs = true
   enable_hevc_demuxing = true
   is_component_build = true
   ffmpeg_branding = "Chrome"
   ```

   Де X,Y,Z Ваші власні значення.  
   Закривши редактор (для редактора vi це - ESC :wq), буде створено теку out/Default, та файл out/Default/args.gn з цими аргументами. Також build.ninja  
   Якщо треба з'ясувати усі можливі аргументи та їх опис то ось весь список:  

   ```
   $ gn args out/Default --list
   ```
8. Для компіляції браузера Chromium команда:  

   ```
   $ ninja -C out/Default chrome
   ```

   Ціль "chrome". Перелік можливих цілей:  

   ```
   $ gn ls out/Default
   ```
9. По закінченню компіляції десь 27000 об'єктів, отримаємо файл out/Default/chrome  
   Котрий можна запустити на виконання.

[![](/assets/images/blog/a7cf1834a794ed0f-4ce36dc6369fe38b.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgb-XyRfUHuzwQAVJbujPCQvguESjNma6zpJRE3YF6IfiyIFsVUvm0aKXpt4815f4bv5_cB9mxMq5iC0wGkYvc1CjML4DcnWRIROd-Qna0D_Mo_yY0MnD8cV6DhbQFf8oC9IGpLdrX5X3mp/s1600/chromium-linux.png)  
*Результат компіляції браузер Chromium (Ubuntu)*

  
  
P.S. Дана публікація є нотаткою для себе що продовжити пізніше, так як на сьогодні 26.09.2016, я не отримав очікуваного результату, h265 файли не програються. А після правок 28.09.2016 вже програються!.  
  

#### P.S. 1.

Але, треба перевірити цей пост:  
[I would like to build Chromium myself with HEVC decoder. Can you share your patches?](http://chromium.woolyss.com/#comment-1297)   
try this <http://pastebin.com/RMUqp2bU>    

```
#file src/third_party/ffmpeg/ffmpeg_generated.gni
#append to your condition

ffmpeg_c_sources += [
    "libavcodec/hevc.c",
    "libavcodec/hevc_cabac.c",
    "libavcodec/hevc_data.c",
    "libavcodec/hevc_filter.c",
    "libavcodec/hevc_mvs.c",
    "libavcodec/hevc_parse.c",
    "libavcodec/hevc_parser.c",
    "libavcodec/hevc_ps.c",
    "libavcodec/hevc_refs.c",
    "libavcodec/hevc_sei.c",
    "libavcodec/hevcdsp.c",
    "libavcodec/hevcpred.c",
    "libavcodec/x86/hevcdsp_init.c",
    "libavformat/autorename_libavformat_hevc.c",
    "libavformat/hevcdec.c"
]
ffmpeg_yasm_sources += [
    "libavcodec/x86/hevc_deblock.asm",
    "libavcodec/x86/hevc_idct.asm",
    "libavcodec/x86/hevc_mc.asm",
    "libavcodec/x86/hevc_res_add.asm",
    "libavcodec/x86/hevc_sao.asm",
    "libavcodec/x86/hevc_sao_10bit.asm"
]


#files src/third_party/ffmpeg/chromium/config/YOUR_BRAND/win/YOUR_ARCH/config.asm + config.h
#add or replace some parameters

#define FFMPEG_CONFIGURATION "******** --enable-decoder='hevc' --enable-demuxer='hevc' --enable-parser='hevc'"
#define CONFIG_HEVC_DECODER 1
#define CONFIG_HEVC_DEMUXER 1
#define CONFIG_HEVC_PARSER 1
```

За цим напрямком знашйов що можна створити власні конфігураційні   
"ffmpeg\_branding":
  

```
$cd third_party/ffmpeg
```

Підправити  "chromium/scripts/build\_ffmpeg.py" line ~553  

```
  # Google Chrome & ChromeOS specific configuration.
  configure_flags['Chrome'].extend([
      '--enable-decoder=aac,h264,mp3,hevc',
      '--enable-demuxer=aac,mp3,mov,hevc',
      '--enable-parser=aac,h264,mpegaudio,hevc',
  ])
```

```
$ cd chromium/scripts
```

```
$ ./build_ffmpeg.py linux x64 --branding "Chrome"
$ ./copy_config.sh
```

Дані конфігурації будуть збережені до теки
  
"third\_party/ffmpeg/chromium/scripts/chromium/config/Chrome/linux/x64"  
тепер можемо створити свій branding: ChromeL  
Скопіювавши нові конфігуаційні файли до теки "third\_party/ffmpeg/chromium/config"  
І підправити out/Default/args.gn зміною ffmpeg\_branding = "ChromeL"  
та "third\_party/ffmpeg/ffmpeg\_options.gni" line 43:  

```
assert(ffmpeg_branding == "Chromium" ||
       ffmpeg_branding == "Chrome" ||
       ffmpeg_branding == "ChromeL" ||
       ffmpeg_branding == "ChromiumOS" ||
       ffmpeg_branding == "ChromeOS")
```

  

```
$ gn args out/Default
```

  

#### P.S. 2: Є результат!!! 28.09.2016

Тепрер мій браузер Chromium v.55.0.2874.0 (linux x64) може програвати HTML5 відео кодоване форматом h265, HEVC.  

[![](/assets/images/blog/40624b3ed8f7ec3d-bd8e33d9d03eadbd.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjV_Ou1_XjJ8dT_WhusCT1onEejVbqPwMaPJHbMwJfj8lu-eIOiIp3DNCW5w7iLzj3_IOTS4WR45ZLh_YggOS9AbAa6aw6B5l_W3ca4PhT-5B7sIU5Cx2oXz17OtqOmX-6NPVdBcmlgiUAY/s1600/cromium-h265.png)  
*Chromium browser 55.0.2874.0 (linux x64) can play HTML5 video h265, hevc*

  
Ось мої правки до станданої компіляції:  
  

```
$ cd third_party/ffmpeg
```

Додав до файлу "ffmpeg\_generated.gni" наступні рядки:  
  

```
ffmpeg_c_sources += [
   "libavcodec/hevc_filter.c",
   "libavcodec/hevc.c",
   "libavcodec/x86/hevcdsp_init.c",
   "libavcodec/hevc_parser.c",
   "libavcodec/hevc_sei.c",
   "libavcodec/hevc_data.c",
   "libavcodec/hevc_ps.c",
   "libavcodec/hevc_mp4toannexb_bsf.c",
   "libavcodec/hevcpred.c",
   "libavcodec/hevc_parse.c",
   "libavcodec/hevc_cabac.c",
   "libavcodec/hevcdsp.c",
   "libavcodec/hevc_refs.c",
   "libavcodec/hevc_ps_enc.c",
   "libavcodec/hevc_mvs.c",
   "libavformat/hevcdec.c",
   "libavcodec/x86/bswapdsp_init.c",
   "libavcodec/bswapdsp.c"
]

ffmpeg_yasm_sources += [
   "libavcodec/x86/hevc_res_add.asm",
   "libavcodec/x86/hevc_mc.asm",
   "libavcodec/x86/hevc_sao_10bit.asm",
   "libavcodec/x86/hevc_deblock.asm",
   "libavcodec/x86/hevc_idct.asm",
   "libavcodec/x86/bswapdsp.asm",
   "libavcodec/x86/hevc_sao.asm"
]
```

Отримав цей перелік командами:  

```
$ find . -name 'hevc*.c' |grep -v mips| grep -v arm | grep -v template
$ find . -name 'hevc*.asm' |grep -v mips| grep -v arm
```

Плюс потребували у процесі компіляції додткові файли:  

```
"libavcodec/x86/bswapdsp_init.c"
"libavcodec/bswapdsp.c"
"libavcodec/x86/bswapdsp.asm"
```

```
$ cd third_party/ffmpeg/chromium/scripts
```

Зміни у файлу "build\_ffmpeg.py" рядок десь 553, додаю "hevc":  

```
  # Google Chrome & ChromeOS specific configuration.
  configure_flags['Chrome'].extend([
      '--enable-decoder=aac,h264,mp3,hevc',
      '--enable-demuxer=aac,mp3,mov,hevc',
      '--enable-parser=aac,h264,mpegaudio,hevc',
  ])
```

За допомгою цого файлу я отримав нову конфігураційну теку з новим "ffmpeg branding" за допомогою скрипту "third\_party/ffmpeg/chromium/scripts/copy\_config.sh"  
  
На основі порівнянь створив зміни у фалах:  
third\_party/ffmpeg/chromium/config/Chrome/linux/x64/config.h:  

```
#define FFMPEG_CONFIGURATION .....  --enable-decoder='aac,h264,mp3,hevc' --enable-demuxer='aac,mp3,mov,hevc' --enable-parser='aac,h264,mpegaudio,hevc'
#define CONFIG_BSWAPDSP 1
#define CONFIG_HEVC_DECODER 1
#define CONFIG_HEVC_DEMUXER 1
#define CONFIG_HEVC_PARSER 1
```

  
third\_party/ffmpeg/chromium/config/Chrome/linux/x64/config.h:  

```
%define CONFIG_BSWAPDSP 1
%define CONFIG_HEVC_DECODER 1
%define CONFIG_HEVC_DEMUXER 1
%define CONFIG_HEVC_PARSER 1
```

Використано було FFMPEG\_VERSION "git-2016-09-16-3c7a098".  
Після цих змін запускаю:  

```
$ gn args out/Default
$ ninja -C out/Default chrome
```

  
Тепер можна запускати браузер:
  

```
$ cd out/Default
$ ./chrome
```

  

#### PS. 2017-03-10

1. General Chromium compile Linux (<https://chromium.googlesource.com/chromium/src/+/master/docs/linux_build_instructions.md>)  
2. HEVC support (<https://github.com/henrypp/chromium/blob/master/hevc_support.md>)  
3. За змінами я створив thrid\_party.patch ( diff -ruN third\_party-orig/ third\_party/ > thrid\_party.patch)  
hrid\_party.patch:  
  

```
diff -ruN third_party-orig/ffmpeg/chromium/config/Chrome/linux/x64/config.asm third_party/ffmpeg/chromium/config/Chrome/linux/x64/config.asm
--- third_party-orig/ffmpeg/chromium/config/Chrome/linux/x64/config.asm 2017-03-09 11:44:09.000000000 -0800
+++ third_party/ffmpeg/chromium/config/Chrome/linux/x64/config.asm      2017-03-09 16:29:55.681700140 -0800
@@ -728,7 +728,7 @@
 %define CONFIG_H264_VDA_DECODER 0
 %define CONFIG_H264_VDPAU_DECODER 0
 %define CONFIG_HAP_DECODER 0
-%define CONFIG_HEVC_DECODER 0
+%define CONFIG_HEVC_DECODER 1
 %define CONFIG_HEVC_QSV_DECODER 0
 %define CONFIG_HNM4_VIDEO_DECODER 0
 %define CONFIG_HQ_HQA_DECODER 0
@@ -1204,7 +1204,7 @@
 %define CONFIG_H261_DEMUXER 0
 %define CONFIG_H263_DEMUXER 0
 %define CONFIG_H264_DEMUXER 0
-%define CONFIG_HEVC_DEMUXER 0
+%define CONFIG_HEVC_DEMUXER 1
 %define CONFIG_HLS_DEMUXER 0
 %define CONFIG_HNM_DEMUXER 0
 %define CONFIG_ICO_DEMUXER 0
@@ -2160,7 +2160,7 @@
 %define CONFIG_H261_PARSER 0
 %define CONFIG_H263_PARSER 0
 %define CONFIG_H264_PARSER 1
-%define CONFIG_HEVC_PARSER 0
+%define CONFIG_HEVC_PARSER 1
 %define CONFIG_MJPEG_PARSER 0
 %define CONFIG_MLP_PARSER 0
 %define CONFIG_MPEG4VIDEO_PARSER 0
@@ -2226,3 +2226,4 @@
 %define CONFIG_LIBRTMPTE_PROTOCOL 0
 %define CONFIG_LIBSSH_PROTOCOL 0
 %define CONFIG_LIBSMBCLIENT_PROTOCOL 0
+%define FFMPEG_CONFIGURATION "******** --enable-decoder='hevc' --enable-demuxer='hevc' --enable-parser='hevc'"
diff -ruN third_party-orig/ffmpeg/chromium/config/Chrome/linux/x64/config.h third_party/ffmpeg/chromium/config/Chrome/linux/x64/config.h
--- third_party-orig/ffmpeg/chromium/config/Chrome/linux/x64/config.h   2017-03-09 11:44:09.000000000 -0800
+++ third_party/ffmpeg/chromium/config/Chrome/linux/x64/config.h        2017-03-09 16:32:54.625407634 -0800
@@ -744,7 +744,7 @@
 #define CONFIG_H264_VDA_DECODER 0
 #define CONFIG_H264_VDPAU_DECODER 0
 #define CONFIG_HAP_DECODER 0
-#define CONFIG_HEVC_DECODER 0
+#define CONFIG_HEVC_DECODER 1
 #define CONFIG_HEVC_QSV_DECODER 0
 #define CONFIG_HNM4_VIDEO_DECODER 0
 #define CONFIG_HQ_HQA_DECODER 0
@@ -1220,7 +1220,7 @@
 #define CONFIG_H261_DEMUXER 0
 #define CONFIG_H263_DEMUXER 0
 #define CONFIG_H264_DEMUXER 0
-#define CONFIG_HEVC_DEMUXER 0
+#define CONFIG_HEVC_DEMUXER 1
 #define CONFIG_HLS_DEMUXER 0
 #define CONFIG_HNM_DEMUXER 0
 #define CONFIG_ICO_DEMUXER 0
@@ -2176,7 +2176,7 @@ 
#define CONFIG_H261_PARSER 0
 #define CONFIG_H263_PARSER 0
 #define CONFIG_H264_PARSER 1
-#define CONFIG_HEVC_PARSER 0
+#define CONFIG_HEVC_PARSER 1
 #define CONFIG_MJPEG_PARSER 0
 #define CONFIG_MLP_PARSER 0
 #define CONFIG_MPEG4VIDEO_PARSER 0
diff -ruN third_party-orig/ffmpeg/ffmpeg_generated.gni third_party/ffmpeg/ffmpeg_generated.gni
--- third_party-orig/ffmpeg/ffmpeg_generated.gni        2017-03-09 11:44:09.000000000 -0800
+++ third_party/ffmpeg/ffmpeg_generated.gni     2017-03-09 16:17:28.463038348 -0800
@@ -660,3 +660,30 @@
   ]
 }

+ffmpeg_c_sources += [
+    "libavcodec/bswapdsp.c",
+    "libavcodec/hevc.c",
+    "libavcodec/hevc_cabac.c",
+    "libavcodec/hevc_data.c",
+    "libavcodec/hevc_filter.c",
+    "libavcodec/hevc_mvs.c",
+    "libavcodec/hevc_parse.c",
+    "libavcodec/hevc_parser.c",
+    "libavcodec/hevc_ps.c",
+    "libavcodec/hevc_refs.c",
+    "libavcodec/hevc_sei.c",
+    "libavcodec/hevcdsp.c",
+    "libavcodec/hevcpred.c",
+    "libavcodec/x86/bswapdsp_init.c",
+    "libavcodec/x86/hevcdsp_init.c",
+    "libavformat/hevcdec.c",
+]
+ffmpeg_yasm_sources += [
+    "libavcodec/x86/bswapdsp.asm",
+    "libavcodec/x86/hevc_deblock.asm",
+    "libavcodec/x86/hevc_idct.asm",
+    "libavcodec/x86/hevc_mc.asm",
+    "libavcodec/x86/hevc_res_add.asm",
+    "libavcodec/x86/hevc_sao.asm",
+    "libavcodec/x86/hevc_sao_10bit.asm",
+]
```

2.1 Зробимо зміни за патчем у теці third\_party:  

```
patch -p1 -i ../thrid_party.patch -d third_party
```

  
Перекомпілюємо:  

```
ninja -C out/Default chrome
```

  
Тепер повинна бути підтримка hvec  
  

[![](/assets/images/blog/25aa8b9710fdaab7-dc19699400b5087d.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi8ptlZL6O_R9Mj0OW0JGbHOdiHeoZe14EF6cOTm4KAW_WMBNCCoW9wAB4crVLYdC2Tm7X6bMLdwGNFTCA5wsw90TYY3QN3aQEkgmJkS-AP79Yyy96CAa1f-Sn9IB9dk1Ze7l3rbzcOVlxC/s1600/chromium02.png)  
*Версія 59.0.3036.0 (Конструкція розробника) (64-розрядна версія)*

  

[![](/assets/images/blog/1cb9ea9024afdd04-8712a3a8c58f73c5.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiM740_WloaNgFRlF3T0FUWSPXuyWzTVvW-syY6fbeu1KHCSl0F71uBz5idDU5e76y0blyueiFAGUiZKpRIOF3vsrfeKVmMutdKGxfCcNNQ9HlpNOvz_Yfq21F53bYaW7OUf8bcSaC_lHzU/s1600/chromium-01.png)  
*Версія 59.0.3036.0 with x265 (HVEC)*
