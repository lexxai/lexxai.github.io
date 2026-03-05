---
layout: post
title: "Нотатка: concurrent.futures.ProcessPoolExecutor  pyinstaller та python.  freeze_support()"
date: 2023-09-11 20:41:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2023/09/concurrentfuturesprocesspoolexecutor.html
---

Знайшов проблему використання concurrent.futures.ProcessPoolExecutor з pyinstaller.

Програма випадала у такі помилки.

[![](/assets/images/blog/efdcd20abb2dffe3-e475d6e1593be5aa.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjpwLnWTm45q3RvZ8Unwa5rFSJa71Yrd8qxOIfT2uX9BxvQuWXEELIzyfnj7-VKhcfS3XS1jjZByIgpIN-osVlupsAEJqzh2MauVti186yRMb35wOdONjS5zaOrTJtlkAoKBDF8Hk4ZyCKKMh1y0nTYB2avp56C2Mdl81of5c7C0yAzd-P8iR4tGsb-g2UR/s1082/Screenshot%202023-09-11%20233504.png)  
*concurrent.futures.ProcessPoolExecutor з pyinstaller*

  

За рішенням: <https://stackoverflow.com/questions/28631288/concurrent-futures-works-well-in-command-line-not-when-compiled-with-pyinstal>

Допомогло використання:

```
from multiprocessing import freeze_support


def main()
    freeze_support()
    ...
```
