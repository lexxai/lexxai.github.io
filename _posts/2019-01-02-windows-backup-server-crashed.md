---
layout: post
title: "Windows backup server crashed"
date: 2019-01-02 23:08:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2019/01/windows-backup-server-crashed.html
---

Буває ситуація коли windows backup server crashed при спробі налатувати.  
Я виявив це буває коли місце призначення було змінено чи щось з наявністю дисків.  
Рішень багато не знайшов, тому знайшов для себе просте рішення зачистити каталог  командою:  

```
wbadmin delete catalog
```

Після цього налаштування починає працювати.
