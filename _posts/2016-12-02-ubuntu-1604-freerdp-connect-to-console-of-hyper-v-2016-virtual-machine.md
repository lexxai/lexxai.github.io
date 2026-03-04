---
layout: post
title: "Ubuntu 16.04. FreeRDP connect to console of Hyper-V 2016 virtual machine."
date: 2016-12-02 03:15:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2016/12/ubuntu-1604-freerdp-connect-to-console.html
---

Керуючись публікацією [Using FreeRDP to connect to the Hyper-V console - Cloudbase Solutions](https://cloudbase.it/using-freerdp-to-connect-to-the-hyper-v-console/)  
Під'єднався з Ubuntu через SSH тунель до Hyper-V console. На базі Hyper-V 2016.   
  

1. Визначаємо ID віртуальної машини "Server2008". Через RDP підключаємось до "HVServer". В консолі запускаємо PowerShell:   
   Get-VM Server2008 | Select-Object Id  
   Id  
   --  
   3327c485-12da4-4c1c-22ac-17f8fc378bb6
2. Ubuntu: створюємо тунель і прокидаємо порт 2179 локально.   
   SSH tunel 127.0.0.1:2179  - > HVServer:2179  
   ssh -L 2179:HVServer:2179 user@example.com
3. Підключаємось:  
   xfreerdp /cert-ignore /vmconnect:3327c485-12da4-4c1c-22ac-17f8fc378bb6 /v:127.0.0.1 /u:administrator

[![](/assets/images/blog/2828a51c353c98f4-ed6fbc6302fc7b41.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj9sNfmgBFRSLLp1KTPR7uKWBimbYPmo32u6NkSD2AeLLN_a-jVidfcWXbx6KViKvAbPofdSUqOMLd-D69Y6YzJs_ANHaZt7Ss-sjg9foPC46xDDG_zmTNtetQiF0L-trTa-9gnkZUz3cQ9/s1600/FreeRDP-vconnect.png)  
*Результат під'єднаної віртуальної машини "Server2008" у Ubuntu 16.04*
