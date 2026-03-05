---
layout: post
title: "Performance Comparison of Python Function Implementations for Equivalent Output"
date: 2024-11-13 21:47:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2024/11/performance-comparison-of-five-python.html
---

😄 Цікаво інколи оптимізувати код.  
Отримав такі рішення для випадку коли не завжди є рядок з всіма параметрами і його треба розбити на частини.  
  
📅 Рядок category може бути "W", "P-V-00", "S", "P-V-01", "L-X", "L-X-A-B-C-D"  
Проведено 10\_000\_000 замірів 8 разів і отримано середні значення часу виконання.

[![](/assets/images/blog/ff0af7637e58ccd9-946bd7cd45c2c458.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhLsOvZMJGfGGssm3hJ8zPwqAWbKbOYlHk6GYfwYqDsBx9trR5OcFRKWCbpxaqlTchm4SkuaTfvFVfr35syWTNkDvmkstdXDj8SBGnHwMcoL_oEkmVHTrVYskfiTHO3s6GHEsY6f9mtdGbtmcrnn8M9FMGD9f3sy56fL64qxoL9ZZGJ08dXjgyAkB8FFOD2/s3600/performance_comparison.png)  
*Perfomance Comparison*

🔖Наведено топ 3 результати вимірювань.

1. Як не дивно, на першому місці
   Version 5, коли є всі елементи в категорії, але на останньому коли не
   всі елементи є - вітання до try-except.   
   Можна провести алегорію з
   приказками "Як тривога, то до Бога", "Без біди Бога не кличуть".
2. На другому місці Version 6 та Version 9 в інших випадках.

[![](/assets/images/blog/a44033f738895b49-bea6595ab5e01542.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhiAq4o1xS-TUCNh1lHWYe1iK6bg25CIDWNZw6uKh9-iMfT98_ckEHnhk2SFk_CkPaBNyzato25ibZoo_f59uBH_sUOv6iSrsvZAMH0aN0uH3OFDN73Jq1NOE2zTiKheNY8jBdcajkFc36PicfX8vPASbdpVuvtkw3bqeOfi_7h3AGiZyGfA9FcMLvN8Zwx/s4092/code.png)  
*versions code*
