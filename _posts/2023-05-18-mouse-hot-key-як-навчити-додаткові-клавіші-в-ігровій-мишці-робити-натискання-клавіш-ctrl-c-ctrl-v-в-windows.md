---
layout: post
title: "Mouse Hot Key. Як навчити додаткові клавіші в ігровій \"мишці\" робити натискання клавіш Ctrl-C, Ctrl-V в Windows."
date: 2023-05-18 04:34:00 +0000
tags: ["action", "AutoHotkey", "mouse"]
blogger_orig_link: https://lexxai.blogspot.com/2023/05/ctrl-c-ctrl-v-windows.html
---

### Що потрібно

1. Ставимо додаток [AutoHotkey](https://www.autohotkey.com).
2. Створюємо файл "BrowserNavigation.ahk"
3. Клац по файлу, і активується зелена іконка (H)

[![](/assets/images/blog/464c21bda1a5875a-7a99cae9d2de3ff1.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhSm6oaCU3ERUrXQ9vN25IFU4l7qOLkXe61L5KwRgTFqjE-X5z8ozNrhUs0lmAaTztbDYWIllDpg7O7nAdjXLMeSizbab8fclKYzUn_-0HS5vuEuTh4JfIOzg8VCRl9n0m1mHkznOm9PLLcok3M2SRekl7S9ukTX2k1q6WgeWriMl3k4UCbcaXnNQ_izx7G/s1280/photo_2023-05-18_07-26-31ED.JPG)  
*Додаткові клавіші для Мишки як Ctrl+C, Ctrl-V*

BrowserNavigation.ahk

```
#Requires AutoHotkey v2.0
Ctrl(k) {
        if GetKeyState("LCtrl", "P"){
            SendInput "{LCtrl up}"
        }else{
            SendInput "{LCtrl down}" . k . "{LCtrl up}"
        }
}
XButton1::  Ctrl("v")
XButton2::  Ctrl("c")
+XButton1:: Ctrl("z")
+XButton2:: Ctrl("y")
```

### Ось в все, є 4 варіанти роботи:

1. Button1  - це  Ctrl+V
2. Button2  - це  Ctrl+C
3. Button1 + Shift - це Ctrl+Z
4. Button2 + Shift - це Ctrl+Y
