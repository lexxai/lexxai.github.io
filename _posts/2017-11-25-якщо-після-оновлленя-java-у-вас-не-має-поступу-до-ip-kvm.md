---
layout: post
title: "Якщо після оновлленя Java у Вас не має поступу до ip-KVM"
date: 2017-11-25 22:43:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2017/11/java-ip-kvm.html
---

Час іде,  світ розвивається, і захищається. Тому Java принципово і глобально вирішило заборонити використання не надійні методи з'єднання та шифрування.  
Так  як більшість IP-KVM використовують для підключення Java клієнти, а програмне забезпечення у IP-KVM не своєчасно оновлене, то у підключенні до сервера IP-KVM буде відмовлено за причини використання застарілого методу MDwithRSA.  

[![](/assets/images/blog/a9f22d11161f65e0-1a56aaf3d2cad9e0.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhIGZ_eFOgqpdyYBxqO03W2gWGfBq709YtxaKYv5p_6EsJHGEJ-zJn894fL-ytSJGQ32LcF9-waCEiYZDLgndRf_ymxiLeyjrrPZ3bxx440I6ORqhqN8guvk7KsQAPYuuxP4utM5IMRo_ld/s1600/javaRSA-ikvm.PNG)  
*Про відмовлення у з'єднанні*

 Але якщо сильно треба, то за матеріалами [Zhaojun's Blog](https://wuzhaojun.wordpress.com/2017/05/05/a-workaround-to-fix-unsigned-jnlp-issue-after-upgrade-java-to-version-8-update-131/),  то тимчасово можна відмінити це обмеження, відредагувавши файл "C:\Program Files (x86)\Java\jre1.8.0\_151\lib\security\java.security" для версії Java 8 update 151.  
Знайшовши файл рядок: jdk.jar.disabledAlgorithms=MD2, MD5, RSA keySize < 1024  
  

[![](/assets/images/blog/0a051dff51a8dd7a-90f94156440cf38c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjRyT7ovumcw5FMGga3Iu1sdz_Z3tgiTVaB8dD8oMMiPNsxBIh9jWutsNGP5h6VozGJFXvljzaFl9xofSAhBaTCLs_U0j_yT87yb9I7DM9ru7G8U9GjtlM5qxQzSr5nc_-MLcP0rYL4FqM4/s1600/javaRSA-ikvm-02.PNG)  
*Значення у файлі конфігурації*

 І змінити можна закоментувати цей рядок, або придбати MD5.  

[![](/assets/images/blog/84601336584238ad-a9d45c0f56f990be.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhQzyhD20X-Loe1kzJPI4ZTqBo5t8idnc1vvbvgFcSuSHrJ_FifOxX5Bm5VJ57OLxDJIXb_7homNHYp5sx2cKq4m323Fc2b5EvEV7D2aSej0YoCsb1mvP2KQu5aLzzVg875peSvL9uLX5-A/s1600/javaRSA-ikvm-01.PNG)  
*Змінення файлу конфігурації*

  
Інші посилання : [Если после обновления Java не работает ip-KVM](http://spvd.ru/page/java-ipkvm-md5withrsa-error)
