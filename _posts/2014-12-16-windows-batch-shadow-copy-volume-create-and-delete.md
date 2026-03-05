---
layout: post
title: "Windows batch Shadow Copy Volume, create and delete."
date: 2014-12-16 00:47:00 +0000
tags: ["batch", "shell", "windows"]
blogger_orig_link: https://lexxai.blogspot.com/2014/12/windows-batch-shadow-create-and-delete.html
---

За допомогою цього скрипту є можливість створити тіньову копію для диску, зробити резервне копіювання даних, і видалити тіньову копію по закінченню копіювання.  
  

```
@echo off
rem --- http://lexxai.pp.ua --- 
set tmpfile=tmp.shadow
set disk=e:
vssadmin create shadow /for=%disk% > %tmpfile%

FOR /F "tokens=1,2 delims=:" %%i in ('find "Shadow Copy" %tmpfile%') do (
 if "%%i" == "    Shadow Copy ID" SET SHID=%%j
 if "%%i" == "    Shadow Copy Volume Name" SET SHNA=%%j
)

call :Strip %SHID%
set SHID=%a%
call :Strip %SHNA%
set SHNA=%a%

echo ---------- START BACKUP inside SHADOW COPY --------
echo PATH is %SHNA%
echo ---------- STOP  BACKUP inside SHADOW COPY --------

IF NOT "%SHID%"=="" vssadmin delete shadows /Shadow=%SHID% /Quiet >NUL
del %tmpfile%

goto :EOF

:Strip
set a=%1

:EOF
```
