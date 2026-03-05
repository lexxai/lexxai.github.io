---
layout: post
title: "Nginx, блокуємо доступ по user agents"
date: 2013-06-10 10:29:00 +0000
tags: ["access", "http", "nginx", "www"]
blogger_orig_link: https://lexxai.blogspot.com/2013/06/nginx-user-agents.html
---

![](/assets/images/blog/999b7a911302c7f6-d2a36ca103f4ab78.jpeg)

  
Інколи треба заблокувати доступ до сайту ботам пошукових системам. Блокувати їх можна за user\_agent.  
  

```
        ## Block Software download user agents ##
        if ($http_user_agent ~*  bingbot|AhrefsBot|msnbot|bot) {
                 return 403;
        }
```
