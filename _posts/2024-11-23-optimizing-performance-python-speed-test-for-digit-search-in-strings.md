---
layout: post
title: "Optimizing Performance: Python Speed Test for Digit Search in Strings"
date: 2024-11-23 17:53:00 +0000
tags: ["optimize", "python", "speed", "string", "test"]
blogger_orig_link: https://lexxai.blogspot.com/2024/11/optimizing-performance-python-speed.html
---

Не так давно я [натрапив на пост](https://www.linkedin.com/feed/update/urn:li:activity:7266090976702521344?utm_source=share&utm_medium=member_desktop), де порівнювались різні методи коду для пошуку цифр у рядках у Python. Виникло бажання перевірити реальні швидкості цих методів, тому вирішив самостійно провести експеримент.

Я розглянув, як швидко кожен метод виконується при пошуку цифр коротких рядках, а також порівняємо їхню ефективність з погляду часу виконання.

Надалі, порівнявши кілька підходів, я поділюсь висновками щодо того, який з них є найкращим в умовах реального використання Python для цієї задачі.

[![](/assets/images/blog/2504a5a28c7fa25c-e84e52506598ba9c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEitcLMSJdOR6mrSbAZQLD5pjfYZk9kG1VQ6EVsPhQX3QgG8yOKlgovE1m3auwVjf3k0OGIqa4cJzn1a1h1YZmf5Ovj8ObNjgqVV40Ec8Eb1qq7o_I3uRaUXtnPjlemvLqjwZRShBs4mrnRoSsWipA21HTMj6aBT8Ef5qu1Rx0v0fegzIVMbO2gbXaWuE32j/s1098/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202024-11-23%20%D0%BE%2018.21.42.png)

  

[![](/assets/images/blog/1d201cacc5165f43-bea6595ab5e01542.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg3hbGH7TnnDzHD70izsqosTyDki7MjtOdk6tNNuw-pYPRLXZyN-VjlZJMXrzc6VkRev53oIOhkbvkp9CsNcf03Rs8N-9KCIzisy3cfXn8a-pIQKwIicUWmP-S2kuHv1AcUzF5ypoTOHoaBdtFXciGnMlgCuK0_xmzqwAtDQx02sS_Z1V0aYpF1hvwzcerM/s1238/code.png)  
*Шість версій для порвняння*

  
Ось результати порівняння середнього часу виконання 10 мільйонів спроб з використанням: MacOS, Python 3.13.0:

[![](/assets/images/blog/870c2ee03edf173b-946bd7cd45c2c458.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiIEDkUzqJSFQMvyMAcaFTNOOrRJ_pj-PiJUJyeIYL7zyWwc0J4v26HXKK6wWLEIzV9ItJhjWbpd6t9WQ9tVyWbETjLtGGSrVPEyBdIQfrnqG_Utfh6-T4OvTlANyyUrI4saU-VHcjUT-sU74tHUKD35Q7rLQHpsfEf48Xb2mykw6o7qZXBGYbGJngN2_gv/s3600/performance_comparison.png)  
*Найкращий по швидкості варіант - Version\_6*

Але для мене було здивування, чому 5 версія повільніша за 4 ту, я гадав що *REGEX = re.compile(r"\d+")*, пришвидшує роботу.   
Але після порівняння 5 версій, я додав шосту версію котра стала найкращою і довела що у випадку використання pre compiled Patterns в re не спрацьовує принцип відносності - літак летить на повітря чи повітря летить на літак 😁. І має велику різницю код:

```
re.findall(REGEX, text)
REGEX.findall(text)
```

 

Код метода порівняння:
