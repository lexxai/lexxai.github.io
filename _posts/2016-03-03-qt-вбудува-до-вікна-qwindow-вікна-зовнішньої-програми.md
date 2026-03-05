---
layout: post
title: "Qt. Вбудува до вікна QWindow вікна зовнішньої програми."
date: 2016-03-03 23:20:00 +0000
tags: ["C++", "embedded window", "MPV", "programming", "Qt"]
blogger_orig_link: https://lexxai.blogspot.com/2016/03/qt-qwindow.html
---

[![](/assets/images/blog/6b96435d654bc798-96b4ed0439c4e6e0.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhw2BatqbWkeGqzWoMAcs25falG3yPQUNgLryWfJO9_vRRZJrqBCV9prhM-mhpCpynbDfREF8FA6Fox7WrNDcZ2k5yISsiyAQgv-qzMpXdE41DOQvfgoGYR5laf8kLCH-q5wv8g6XUD8YCJ/s1600/mpv-logo.PNG)

Задача вбудувати роботу зовнішньої програми (mpv) до проекту Qt. Тобто створити вікно QWindow, і прикріпити до нього вікно зовнішньої програми. [За результатами публікації](https://bugreports.qt.io/browse/QTBUG-40320) створено цей код.  
Програма, спершу шукає вікно за назвою вікна "No file - mpv", а якщо не знаходить до за назвою класу "mpv". Так як заголовок вікна може бути різним в залежності від відкритого відео файлу.  
За назвою знаходиться ідентифікатор вікна id, створюється QWindow за цим ідентифікатором. Надалі створюється QWidget за створеним QWindow, та підганяється розмір.    
Після 2х секунд, програма повертає вікно плеєра mpv в відокремлений стан.  
  
embededWindow.pro:  
  

```
QT += core gui
greaterThan(QT_MAJOR_VERSION, 4): QT += widgets
TARGET = embededWindow
TEMPLATE = app
SOURCES += main.cpp
LIBS+= -luser32
```

  
main.cpp:  

```
#include "windows.h"
#include <QApplication>
#include <QWindow>
#include <QWidget>
#include <QDebug>
#include <QTimer>

int main(int argc, char *argv[])
{
QApplication a(argc, argv); // Windows: Find HWND by window title

WId id = (WId)FindWindow(NULL, L"No file - mpv");
if (id) {
  qDebug()<<"Found by tile";
  TCHAR ClassName[MAX_PATH];
  int r=GetClassName((HWND)id,ClassName,_countof(ClassName));
  if (r){
    qDebug()<<"GetClassName"<<r<<QString::fromWCharArray(ClassName);
  }
}else{
  TCHAR ClassName[]=L"mpv";
  id = (WId)FindWindowEx(NULL,NULL,ClassName,NULL);
  if (id) qDebug()<<"Found by ClassName";
}

if (!id) {
  qDebug()<<"not found Window"<<id;
  return -1;
}

// Part 1
 QWindow* window = QWindow::fromWinId(id);
 window->show();
 window->requestActivate();

// Part 2
 QWidget* widget = QWidget::createWindowContainer(window);
 widget->show();
 widget->resize(window->geometry().size());

 QTimer t;
 t.start(2000);

// detach 
 QObject::connect(&t, &QTimer::timeout, [=] {
 window->setParent(nullptr);
 }); 
return a.exec();
}
```

  
  

[![](/assets/images/blog/08cc1a560f0dcb78-77fb3196d1720200.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiDxycpzMTzvUYaKrVQUIYheh0NOugbsfGEeXXnIJ8CpWGtfJBVB2eVPFkHOhqmi0ucxfjGZyho6knddjGc8NCFJTCV6wZ96ltMIF6ldUYSB9ZJ4nrApE0uR0D1cOs36KhpoLAbfm4PwsFa/s1600/mpvplayer.PNG)  
*Оригінальний плеєр mpv*

[![](/assets/images/blog/1b42f5537789e8bd-fd15c1398c36aa0c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjW_Hif6tZEqmFhq1t6fEGqpOznK9i1-PoBsEVIaVMD3s7Xwh4F6MoZji2-xYGvq2yECdZ6sEhyphenhyphen5ehplOLIHUl15-QzFQlbDE5euw1PvEZTfqYzsCmQQzD8kOztul0eP4cG5yn3_oybcOXv/s1600/mpvplayer-embeded.PNG)  
*Embedded плеєр mpv*

[![](/assets/images/blog/b0414832fbcfa529-d5a3ffe1fce32c10.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgGFl0PkYKiYG8-FAQX6aCc_NwMWztqMoB7Bs56TRB1R91Z3pr3sXhV_M_9lChXMGoiFkAp9BPRYXovLj6CHA8C9H7PR7JRxNpT41WsrKYFMwhYbctkF2_-2BLFOstJjX8ZN3LQhNgeqFfy/s1600/mpvplayer-released.PNG)  
*Від'єднаний плеєр mpv*

Використовується додатково user32.dll.
