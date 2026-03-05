---
layout: post
title: "Програвання Video у HTML, використання треків."
date: 2013-06-01 16:18:00 +0000
tags: ["Audio", "html", "html5", "track", "video"]
blogger_orig_link: https://lexxai.blogspot.com/2013/06/video-html.html
---

![](/assets/images/blog/f1a60ad3308197f7-d940045a2deced1a.png)

  
Для створення мультимедійного матеріалів, завжди мені було цікаво забезпечити вільний вибір мови для глядачів/читачів/слухачів.  
Для себе я шукаю або створюю відео фільми рідною мовою, але треба не думати тільки про себе, є і інші мови та комусь зручніше слухати на ній.  
Тому і виникло питання як же більш універсально програвати відео матеріали з використанням декількох треків аудіо чи субтитрів. Інтернет браузер повинен використовуючи данні про мову користувача, автоматично (або за вибором користувача) пропонувати програвати матеріал мовою користувача.  
Варіантів декілька:  

* створити відеофайли окремо для кожної мови, скільки мов стільки і файлів. Практично. Зрозуміло для розробника, за назвою файлу якою мовою файл. Користувач не завантажує надлишкову інформацію, використаний трафік мінімальний. Але розмір усіх файлів стає надлишковий (для розробника) тому що відео дублюється у кожному з файлів.
* створити відеофайл де відео одне, а для для кожної мови свій аудіо трек, скільки мов стільки і треків. Вісі компоненти розміщенні у одному файлі. Для розробника розмір файлу не великий, тому що відео не дублюється, але користувач завантажує надлишкову інформацію з невикористаними аудіо треками на усіх мовах.
* створити відеофайл без звуку, аудіофайли та субтитри окремими файлами. А програвач користувача повинен завантажити ці файли окремо і почати програвати синхронно. Користувач не завантажує надлишкову інформацію, використаний трафік мінімальний, але синхронно відтворити окремі данні може бути проблематично.
* створити відеофайл без звуку, аудіофайли та субтитри окремими файлами. А на серверній стороні медіа сервер автоматично мікшує окремі фали і результатом видає один потік. Користувач не завантажує надлишкову інформацію, використаний трафік мінімальний, додатково сервер може надавати медіа потік у різній якості в залежності від швидкості підключення користувача. Але при цьому на сервер накладаються додаткові розрахунки, необхідно мати спеціальне програмне забезпечення.

## Mетоди та проблемами відтворення відео

Вивчаючи [w3schools.com](http://www.w3schools.com/html/html_videos.asp), можемо ознайомитися з методами та проблемами відтворення відео використовуючи HTML. Головні проблеми це сумісність між різними пристроями (PC, Mac, iPad, iPhone, [Android](http://uk.wikipedia.org/wiki/Android)), та Інтернет браузерами (Internet Explorer, Chrome, Firefox, Safari, Opera).

На сьогодні більшість відео прийнято програвати за допомогою [Adobe (Macromedia) Flash Player](http://uk.wikipedia.org/wiki/Flash_Player) використовуючи наступні коди.

```
<embed src="intro.swf" height="200" width="200">
```

або
  

```
<object data="intro.swf" height="200" width="200"></object>
```

Але якщо браузер не підтримує Flash (наприклад, OS Android 4.x), то відео не буде відтворене.
  

### HTML5

Новий стандарт [HTML5](http://uk.wikipedia.org/wiki/HTML5), описує нові елементи для відтворення відео.  

```
<video width="320" height="240" controls>
  <source src="movie.mp4" type="video/mp4">
  <source src="movie.ogg" type="video/ogg">
  <source src="movie.webm" type="video/webm">
Your browser does not support the video tag.
</video> 
```

Де тег [<source>](http://www.w3schools.com/tags/tag_source.asp), описує альтернативні джерела де браузер повинен вибрати в залежності від типу медіа файлу та підтримуваних кодеків.  
Але якщо ваш застарілий браузер не підтримує [HTML5](http://uk.wikipedia.org/wiki/HTML5), то відео не буде відтворене.  
Найкраще рішення це описати основний та резервні методи відтворення відео.  

```
<video width="320" height="240" controls>
  <source src="movie.mp4" type="video/mp4">
  <source src="movie.ogg" type="video/ogg">
  <source src="movie.webm" type="video/webm">
  <object data="movie.mp4" width="320" height="240">
    <embed src="movie.swf" width="320" height="240">
  </object>
</video> 
```

Таким чином [браузер буде робити спробу відтворити відео](http://www.w3schools.com/html/tryit.asp?filename=tryhtml_video_html5_4) використовуючи один формат з перелічених у наступній послідовності:  

1. HTML5, формат файлу [MP4](http://uk.wikipedia.org/wiki/MP4).
2. HTML5, формат файлу [OGG](http://uk.wikipedia.org/wiki/Ogg).
3. HTML5, формат файлу [WEBM](http://uk.wikipedia.org/wiki/WebM).
4. HTML,  Internet Explorer object формат файлу [MP4](http://uk.wikipedia.org/wiki/MP4).
5. HTML,  Flash Plyer, Mozilla embed

Найпростіше [розмістити відео на сервісі YouTube](http://www.w3schools.com/html/html_youtube.asp), і використати можливості сервісу щодо компонування, кодування та надання різноманітних методів відтворення в залежності від обладнання клієнта.

### Додавання Субтитрів

HTML5 описує спеціальний тег відстеження <track> за допомогою нього можна описувати деякі метадані що зав'язані на час і позицію відтворення відеофайлу. Використовуючи формат [WebVTT: The Web Video Text Tracks Format](http://dev.w3.org/html5/webvtt/) описаний у специфікаціях [W3C](http://www.w3.org/). Є можливість описати: субтитри, описи, назви, навігацію, розділи, деякі метадані.



### WebVTT file format

Файл WebVTT це  простий текстовий файл що має розширення  ".vtt" і має певний формат:  

```
WEBVTT FILE [idstring]
[hh:]mm:ss.msmsms --> [hh:]mm:ss.msmsms
[cue settings] TextLine1 TextLine2
```

Приклад:  

```
WEBVTT
00:11.000 --> 00:13.000
<Roger Bingham>We are in New York City
00:13.000 --> 00:16.000
<Roger Bingham>We're actually at the Lucern Hotel, just down the street
```

Приклади використання WebVTT описані у наступній презентації: "[WebVTT Presentation](http://html5videoguide.net/presentations/WebVTT/#landing-slide)"  

### Використання .vtt

Тег <track> зараз працює у браузерах [Internet Explorer 10](http://ie.microsoft.com/testdrive/ "Internet Explorer 10 download") та [Google Chrome](https://www.google.com/intl/en/chrome/browser/ "Download Google Chrome"). Браузер Firefox підтримку ще [не реалізував](https://bugzilla.mozilla.org/show_bug.cgi?id=629350 "Firefox track element implementation bug report").  
Код HTML використання може бути таким:  

```
<video controls preload="auto" width="640" height="264"> 
 <source src="oceans-clip.mp4" type='video/mp4' /> 
 <source src="oceans-clip.webm" type='video/webm' />
 <source src="oceans-clip.ogv" type='video/ogg' /> 
 <track kind="captions" src="subtitles_en.vtt" srclang="en" label="English" default>
 <track kind="captions" src="subtitles_uk.vtt" srclang="uk" label="Ukrainian">
</video>
```

[![](/assets/images/blog/9cd1e33d71342658-59aa86d80ff19605.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjoKX59lfuB0VnR34o0dHicszQvZDH8eSogi8_7YTGZC5jaAwpNRT5wop3sjdbAJeMDBKYKjfgvCwCbSCuloMFJi_Gq99v3cw-b4egF9T0qWZG6n60llx7JlK_I7mpuvEbkpH2Rsds2-Gjx/s1600/html5-subs-01.png)  
*Приклад використання субтитрів різними мовами, IE 10, HTML5*

Для сумісності з різними браузерами можна використати додаткові можливості різноманітних програмних плеєрів, наприклад цей: "[The open source HTML5 video player](http://www.videojs.com/)". Він написаний на [JavaScript](http://uk.wikipedia.org/wiki/JavaScript) використовує теги HTML5, і за допомогою [скриптів обробляє самостійно фали  .vtt](http://www.videojs.com/docs/tracks/) (WebVTT ) і синхронно показує.  

[![](/assets/images/blog/3a2a191de865e096-c5518876f2ae268b.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjMlR0KCCAKHQ7vGXjt4NuNUQky8gS7XcwJvuG-1im1wMXqW_fyK9P4f7lpSqUExMuE7X6kVEwrGQhVpYfPTZvC5UnELLs7eCEAF-P9Z3yn7D_ePEtbmOMjFHSs7eAJq2Sosfx5c5h0KGD1/s1600/html5-subs-02.png)

  
Ознайомлюючись з навчальним посібником "[Getting started with the HTML5 track element](http://www.html5rocks.com/en/tutorials/track/basics/)", можна знайти додаткові можливості використанні часових міток "cues" .  
Так наприклад можна помітити часовий проміжок певною міткою і потім відтворювати тільки цей проміжок.  

```
var sfx = new Audio('sfx.wav'); 
var track = sfx.addTextTrack('metadata'); 
// previously implemented as addTrack() 
// Add cues for sounds we care about. 
track.addCue(new TextTrackCue(12.783, 13.612, 'dog bark')); 
// startTime, endTime, text 
track.addCue(new TextTrackCue(13.612, 15.091, 'kitten mew')); 
function playSound(id) { 
sfx.currentTime = track.getCueById(id).startTime; 
  sfx.play(); 
} 
playSound('dog bark'); 
playSound('kitten mew');
```

Приклади використання HTML5 track: <http://html5doctor.com/video-subtitling-and-webvtt/>
  
  

### Медіа файли що мають декілька медійних треків

Медіа ресурси можуть мами декілька вбудованих аудіо чи відео треків, наприклад діалоги на різних мовах, чи коментарі режисера. Відео може буде зняте з різник ракурсів.  
[HTML. Living Standard. Media resources with multiple media tracks](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html#the-track-element).  
The `audioTracks` attribute of a
[media element](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html#media-element) must return a [live](http://www.whatwg.org/specs/web-apps/current-work/multipage/infrastructure.html#live) `AudioTrackList` object
representing the audio tracks available in the [media element](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html#media-element)'s [media
resource](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html#media-resource).  
The same object must be returned each time.  
The `videoTracks` attribute of a
[media element](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html#media-element) must return a [live](http://www.whatwg.org/specs/web-apps/current-work/multipage/infrastructure.html#live) `VideoTrackList` object
representing the video tracks available in the [media element](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html#media-element)'s [media
resource](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html#media-resource). The same object must be returned each time.  

In this example, a script defines a function that takes a URL to a video and a reference to an
element where the video is to be placed. That function then tries to load the video, and, once it
is loaded, checks to see if there is a sign-language track available. If there is, it also
displays that track. Both tracks are just placed in the given container; it's assumed that styles
have been applied to make this work in a pretty way!  

```
<script>
 function loadVideo(url, container) {
   var controller = new MediaController();
   var video = document.createElement('video');
   video.src = url;
   video.autoplay = true;
   video.controls = true;
   video.controller = controller;
   container.appendChild(video);
   video.onloadedmetadata = function (event) {
     for (var i = 0; i < video.videoTracks.length; i += 1) {
       if (video.videoTracks[i].kind == 'sign') {
         var sign = document.createElement('video');
         sign.src = url + '#track=' + video.videoTracks[i].id; 
         sign.autoplay = true;
         sign.controller = controller;
         container.appendChild(sign);
         return;
       }
     }
   };
 }
</script>
```

[![](/assets/images/blog/7215261d568e134b-8a4b4157ab39c866.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEirPWmJvg5HPi9_yV2KoZMrBVkd6CPpJwiYVfHMpuC8AKTAGHyQ3Oi43d-Lsbvssq3m3Js1LoK768PAQZPxFp4BAM7ZUT-MelBNTe7kTY5dihkla30nlD1L32u20MlaxOpZQeHW0GdRATZz/s1600/html5-subs-03.png)  
*Return values forAudioTrack.kind()andVideoTrack.kind()*

### При прямому відтворенні можна задати необхідний трек через "Media Fragments URI"

```
<video src="myvideo#track=Alternative"></video>
```

Таким чином можна вибрати для програвання визначений трек за назвою "Alternative"  

### Синхронізація багатьох медіа елементів

Для синхронізації можна використати [MediaController](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html#mediacontroller), котрий може мати кожен медійний елемент. По замовчуванню, елементи  не мають назначеного [MediaController](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html#mediacontroller)а.  
Його створюють для головного елемента, та його підрядних елементів.  
І потім можна контролювати усі підрядні елементи, наприклад sound tracks, і синхронно керувати ними: старт-стоп-позиціювання.
