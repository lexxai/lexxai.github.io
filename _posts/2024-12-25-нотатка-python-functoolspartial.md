---
layout: post
title: "Нотатка. Python. functools.partial."
date: 2024-12-25 22:16:00 +0000
tags: ["python"]
blogger_orig_link: https://lexxai.blogspot.com/2024/12/python-functoolspartial.html
---

🔨 [functools.partial(func, /, \*args, \*\*keywords)](https://docs.python.org/uk/3.13/library/functools.html#functools.partial)

[![](/assets/images/blog/98d6170bde5191f4-bb71bcf15e8922c0.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh06F5zlVp0bKwHRMaEaNDHZA0UREklR-7k4jAWX7p4cdIZc4AflhD0Pn2XROwhwW0GBQfCNSRPGini2uNGDnHJl9FX_Ja5aOxPon6VNuElT0HBZRcx52SPhhGpKGCYvjs9exj-dIDFPk5fODdxwaStjWoQNDrlbiiOqlI3u9mZcVUBDrAMzr-TQwWrthug/s1099/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-12-26%20000018.png)  
*functools.partial*

📍
Повертає новий частковий об’єкт, який під час виклику поводитиметься
як func, що викликається з позиційними аргументами *args*і ключовими
аргументами *keywords*. Якщо до виклику надається більше аргументів, вони
додаються до *args*. Якщо надаються додаткові ключові аргументи, вони
розширюють і замінюють ключові слова.

📌
Примітка. [Більшість функцій планування asyncio не дозволяють передавати ключові аргументи](https://docs.python.org/uk/3.13/library/asyncio-eventloop.html). Для цього скористайтеся *[functools.partial()](https://docs.python.org/uk/3.13/library/functools.html#functools.partial)*:

```
# will schedule "print("Hello", flush=True)"
loop.call_soon(
   functools.partial(print, "Hello", flush=True)
   )
```

🎧 Використання часткових об’єктів зазвичай зручніше, ніж використання
[лямбда-виразів](https://acode.com.ua/lambda-python/), оскільки asyncio може краще відтворювати часткові
об’єкти в повідомленнях про налагодження та помилки.

```
# лямбда-вираз
loop.call_soon(
   lambda : print("Hello")
   )
```
