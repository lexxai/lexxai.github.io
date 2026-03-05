---
layout: post
title: "Нотатка для себе: JavaScript - скільки років пройшло."
date: 2016-11-01 18:04:00 +0000
tags: ["JavaScript", "programming", "програмування"]
blogger_orig_link: https://lexxai.blogspot.com/2016/11/js-years-ago.html
---

Потрібно було написати маленький код щоб, сторінка історичних фактів була завжди актуальна, на кшталт: 23 роки тому.  До того ж код відрізняв різні мови сайту, за адресою сторінки: /uk/...., /en/....   
Проблема стала в тому що цей код повинен працювати на форумі, де є обрізання службових символів таких як: <, >.

```
<script>function toD(yy){var te,te1,d1,texf,pastext,lang,yt;var parser=document.createElement('a');parser.href=document.URL;lang=parser.pathname.split('/',2)[1].toLowerCase();d1=new Date();d1.setFullYear(yy,0,1);te=new Date().getFullYear()-d1.getFullYear();if(te==0)return;te1=te-(Math.floor(te/10)*10);switch(lang){case"ru":yt="лет";switch(te1){case 1:yt="год";break;case 2:case 3:case 4:yt="года";break;} pastext=yt+" назад";break;case"en":yt="years";if(te1==1)yt="year";pastext=yt+" ago";break;default:yt="років";switch(te1){case 1:yt="рік";break;case 2:case 3:case 4:yt="роки";break;} pastext=yt+" тому";} texf=" ("+te+" "+pastext+") ";document.write(texf);return te;}</script>
```

Використання:  

```
<script>toD(2000)</script>
```

Наприклад, 2000 рік це було:
