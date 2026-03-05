---
layout: post
title: "Нотатка. Python, Django, get_file та кеш браузера."
date: 2024-10-29 14:43:00 +0000
tags: ["cache", "Django", "http", "nginx", "python"]
blogger_orig_link: https://lexxai.blogspot.com/2024/10/python-django-getfile.html
---

🚰 Якщо потрібно віддавати певні файли засобами Django view, а не як статичний файл через NGINX. То не забуваємо додавати заголовки про інформацію для кешування браузерами, щоб не перевантажувати запитами сервер.

[![](/assets/images/blog/46fe3baf1530185b-bea6595ab5e01542.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiwdKwQBtpBwR2vuolPXx5I5G4CB8-ah6NkOwO2U5QzcaADk_0X7VAr6b1VtVlGVrylA8b71_Qk7Nk2bCXiZPsFMlGCTlnJZyc2-4dPrpUH9XJ2uvhQ7kESJPQ-rKwJmLx0-xzMgiyhzYncvqnL4g1FWL_XOjj8jVlQry44_aD2sfUAjFMnIrsZJJFw8x1J/s1364/code.png)
