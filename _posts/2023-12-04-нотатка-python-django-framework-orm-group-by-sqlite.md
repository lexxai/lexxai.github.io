---
layout: post
title: "Нотатка: Python. Django framework. ORM \"GROUP BY\". SQLite."
date: 2023-12-04 03:49:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2023/12/python-django-framework-orm-group-by.html
---

### Django SQL GROUP BY.

А Ви знали що його не має в ORM у прямому вигляді ?

```
SELECT x.* FROM logs x WHERE x.username = 'user1' GROUP BY x.host
```

### DBeaver тестування

Є таблиця log доступу користувачів:

```
CREATE TABLE logs (
	id INTEGER PRIMARY KEY AUTOINCREMENT,
	date DATETIME,
	host VARCHAR,
	request VARCHAR,
	username VARCHAR
);

CREATE INDEX logs_host_IDX ON logs (host);
```

[![](/assets/images/blog/6c796d6761b884a5-d45cd367ec300fd5.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEijcs_pKCSt5T4xyHSLJK4e8fXmsFLwMLM_49DaKj2glA5x9e7UtltYvT9FE-q-EBSobgyHu71c5rmdBAh8UOMDBK1AhBDxKHYAHbFD__T8GkKHKww715kUtPqW1kr3DG5zfLuoZD2fjcZ891h5sfklL2UgTGjx8aiY99gM6xN9A1ClJSN6P3JLSTK2Uxwd/s677/Screenshot%202023-12-03%20132703.png)  
*SEED LOG TABLE*

Стала проста задача для SQL запиту. Для отримання унікальних записів IP адрес
з яких отримував доступ певний користувач.

  

```
SELECT x.* FROM logs x WHERE x.username = 'user1' GROUP BY x.host
```

DBeaver:

[![](/assets/images/blog/554b44c92b4addd8-9918986306ba4302.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjluDseHAYPaiXgGOjnxQYwJTiK5nzTrwV-_4ga5IM0Wboh4luPZ1XvlSLlpItKaWyDAKjbWYvCEmA-cU2wxaiUuVNtXFasiUvnUSU1gPDUpdkRJFnjovDEoesEUU0J2Lcp2k9kwDXy-eb3OJZsY1VEb9n56VZZVQiscavpJiSi9VwuQIRsnQpiRXgDWhFJ/s802/Screenshot%202023-12-03%20132741.png)  
*DBeaver SQL 'GROUP BY'*

### DJANGO реалізація.

### 

```
> poetry init
> poetry shell
> poetry add django
> django-admin startproject groupby  
> cd groupby  
> python manage.py migrate
```

groupby\loganalyze\models.py:

```
class Log(models.Model):
    date = models.DateTimeField(default=timezone.now)
    host = models.CharField(max_length=128)
    request = models.CharField(max_length=128)
    username = models.CharField(max_length=128)

    class Meta:
        indexes = [
            models.Index(fields=["host"], name="host_idx")
        ]
```

```
> python manage.py makemigrations  
Migrations for 'loganalyze':  
  loganalyze\migrations\0001_initial.py  
    - Create model Log
```

```
> python manage.py migrate  
Operations to perform:  
  Apply all migrations: admin, auth, contenttypes, loganalyze, sessions  
Running migrations:  
  Applying loganalyze.0001_initial... OK 
```

Далі на GitHub
<https://github.com/lexxai/python_django_groupby>

[![](/assets/images/blog/672aba71877a230e-cedc64bc6c4745bf.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiKUkETQTG2qOIVcNzVR4Vw2eXGyVn1p4gTg_DpN_Pn7v9lQMB4oIv_6w3F1-IPOCN8d7gcUsMQyU7PfCGBqSvoK3_zUMr4fLY_EL8Sdakt3U3fHgUy1ruvrYEXjyDqDSyQT2JqJICFR13XjnMGZKpwQ_d9qvryTYA2Lo4-FxCSS-elO2F_5bDsY_57iI7w/s1912/web-02.png)  
*Тільки користувач "user1"*

[![](/assets/images/blog/27a02aedca42bf56-cc8c90a96ea3dd60.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjY8tyRNiuUfF5ocqXX_cBHyBz3deB3iRayefROwOdg6nMzUpvXVIduvEjYSm4qdoU3_m3mZj6j2-vHLyYR8X8J82b3-F09Fdr4MMNuS5wHInWce2Puxc7ICg_JDAU-cvU8SAoVSfsP1d-V_HwbHiGT2Zi4DssB-huCVwWU6mMhY8yrsgPrZdwfrZdeINgR/s1913/web-03.png)  
*Тільки користувач "user1"*

```
    # Filter "user1" only  
    data0 = Log.objects.filter(username__exact = "user1")  
      
    # Select from previous result only column with name 'host' and get unique value for it with   
    # calculate minimum value on 'id' column, it should be first raw only.  
    grouped_records = data0.values('host').distinct().annotate(min_id=Min("id"))  
  
    # From clear table select founded rows by founded "id" lists.  
    data =  Log.objects.filter(id__in=grouped_records.values_list("min_id", flat=True))
```

[![](/assets/images/blog/234fbce2b2449781-9dd4454593c274f2.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjZmiV5Hh8QbCMo5VdA-DXjh8eAyLMzTzMRB2IpRXKvmE0Qz5eo0qgqCn8FhSlgWSLIcfGxV2MYh6ORTeaw1g2gPnvWoAE5AXzdtFT_YPjeIJKXQx_j30S_PEZ_6ew7kMditU-v1hYRo_FPdLN1eFsmpTvfp1hjSoGWxis06RJJxe-X3u14I7vh6pzFXhgp/s1913/web-04.png)  
*Тільки користувач "user1" з унікальними IP адресами*

Django log:

```
December 04, 2023 - 05:15:32  
Django version 4.2.7, using settings 'groupby.settings'  
Starting development server at http://127.0.0.1:8000/  
Quit the server with CTRL-BREAK.  
  
(0.000) SELECT "loganalyze_log"."id", "loganalyze_log"."date", "loganalyze_log"."host", "loganalyze_log"."request", "loganalyze_log"."username" FROM "loganalyze_log" WHERE "loganalyze_log"."id" IN (SELECT DISTINCT MIN(U0."id") AS "min_id" FROM "loganalyze_log" U0 WHERE U0."username" = 'user1' GROUP BY U0."host"); args=('user1',); alias=default
```

<https://github.com/lexxai/python_django_groupby>
