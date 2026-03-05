---
layout: post
title: "Перші спроби багатопотокового програмування Qt5 + OpenMP +MS  Visual Studio"
date: 2015-05-14 19:23:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2015/05/qt5-openmp-ms-visual-studio.html
---

Продовжуючи [вивчати багатопотоковість](http://www.lexxai.pp.ua/2015/05/qt5.html) з'ясував що бібліотека [libraw](http://www.libraw.org/) має підтримку багатопотокового програмування - [OpenMP.](http://uk.wikipedia.org/wiki/OpenMP)  
  

[![](/assets/images/blog/b033fbf5d7c5c192-0ffe979bede4ec31.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjAHEn8WX6cG1T_e48D70X-NPrgJCNAlNEJf0V8kJTQVVgU_mIsVQEozQqiDRUfQ_nT6smPjxIbpXrvaQMzWzA-PZcMnIzuojyG7Sm-sXH6hvFtMnio4UFhV_aQwrlWsJQAus8QkeLDDJvF/s1600/openmp.jpg)

Для використання потрібно дозволити компілятору використовувати [OpenMP](http://openmp.org/wp/openmp-compilers/) у  випадку [MS  Visual Studio](https://www.visualstudio.com/) це вмикається так : [OpenMP in Visual C++](https://msdn.microsoft.com/en-us/library/tt15eb9t.aspx), [/openmp (Enable OpenMP 2.0 Support)](https://msdn.microsoft.com/en-us/library/fw509c3b.aspx)  
  
  
Після чого директиви  #pragma omp починають працювати. Таким чином я перекомпілював  libraw.dll з підтримкою omp.  
  
Для вмикання  [OpenMP](http://uk.wikipedia.org/wiki/OpenMP) у середовищі [Qt5](https://wiki.qt.io/Qt_5) для компілятора [MS  Visual Studio](https://www.visualstudio.com/)  
у файлі проекту (.pro) потрібно додати:  

```
QMAKE_CXXFLAGS += -openmp
```

Та пере компілювати проект.  
  
Для тестування написав цей код:  

```
#pragma omp parallel
{
  qDebug("OpenMP parallel, thread=%d of %d",omp_get_thread_num(),omp_get_max_threads());
}
#pragma omp parallel for 
    for (int i=0;i<16;i++){
        qDebug("1. OpenMP i=%d, thread=%d of %d",i,omp_get_thread_num(),omp_get_max_threads());
        qDebug("2. OpenMP i=%d, thread=%d of %d",i,omp_get_thread_num(),omp_get_max_threads());
        qDebug("3. OpenMP i=%d, thread=%d of %d",i,omp_get_thread_num(),omp_get_max_threads());
    }
```

Отримав результат автоматичного розподілення операцій циклу у по різним потокам. Результат:
  

```
OpenMP parallel, thread=0 of 8
OpenMP parallel, thread=5 of 8
OpenMP parallel, thread=1 of 8
OpenMP parallel, thread=6 of 8
OpenMP parallel, thread=3 of 8
OpenMP parallel, thread=4 of 8
OpenMP parallel, thread=2 of 8
OpenMP parallel, thread=7 of 8
1. OpenMP i=1, thread=0 of 8
1. OpenMP i=0, thread=7 of 8
1. OpenMP i=2, thread=2 of 8
1. OpenMP i=3, thread=4 of 8
1. OpenMP i=4, thread=1 of 8
2. OpenMP i=1, thread=0 of 8
1. OpenMP i=6, thread=6 of 8
1. OpenMP i=7, thread=3 of 8
2. OpenMP i=0, thread=7 of 8
1. OpenMP i=5, thread=5 of 8
2. OpenMP i=2, thread=2 of 8
2. OpenMP i=3, thread=4 of 8
2. OpenMP i=4, thread=1 of 8
3. OpenMP i=1, thread=0 of 8
2. OpenMP i=6, thread=6 of 8
2. OpenMP i=7, thread=3 of 8
3. OpenMP i=0, thread=7 of 8
2. OpenMP i=5, thread=5 of 8
3. OpenMP i=2, thread=2 of 8
3. OpenMP i=3, thread=4 of 8
3. OpenMP i=4, thread=1 of 8
1. OpenMP i=8, thread=0 of 8
3. OpenMP i=6, thread=6 of 8
3. OpenMP i=7, thread=3 of 8
1. OpenMP i=9, thread=7 of 8
3. OpenMP i=5, thread=5 of 8
1. OpenMP i=10, thread=2 of 8
1. OpenMP i=11, thread=4 of 8
1. OpenMP i=12, thread=1 of 8
2. OpenMP i=8, thread=0 of 8
1. OpenMP i=13, thread=6 of 8
1. OpenMP i=14, thread=3 of 8
2. OpenMP i=9, thread=7 of 8
1. OpenMP i=15, thread=5 of 8
2. OpenMP i=10, thread=2 of 8
2. OpenMP i=11, thread=4 of 8
2. OpenMP i=12, thread=1 of 8
3. OpenMP i=8, thread=0 of 8
2. OpenMP i=13, thread=6 of 8
2. OpenMP i=14, thread=3 of 8
3. OpenMP i=9, thread=7 of 8
2. OpenMP i=15, thread=5 of 8
3. OpenMP i=10, thread=2 of 8
3. OpenMP i=11, thread=4 of 8
3. OpenMP i=12, thread=1 of 8
3. OpenMP i=13, thread=6 of 8
3. OpenMP i=14, thread=3 of 8
3. OpenMP i=15, thread=5 of 8
```
