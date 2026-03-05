---
layout: post
title: "Blogger to GitHub Pages Sync Tool (blog2ghp)"
date: 2026-03-04 18:10:00 +0000
tags: ["GitHub", "GitHub Actons", "multithread", "python", "sync"]
blogger_orig_link: https://lexxai.blogspot.com/2026/03/blogger-to-github-pages-sync-tool.html
---

#### Навіщо?

Я вирішив отримати практичний досвід роботи з GitHub Actions та GitHub
Pages.

Мій блог має RSS-стрічку, і це дало ідею: якщо автоматично обробляти
RSS-сторінки, можна отримати повну резервну копію блогу у форматі Markdown.

А вже цей Markdown легко опублікувати в репозиторії GitHub Pages / Jekyll.

Таким чином я отримую:

* автоматичний бек-ап контенту
* контроль над контентом у Git
* можливість міграції з Blogger без втрат
* статичну версію блогу

#### Приклад використання

[![](/assets/images/blog/e9358132cda8c4c2-e98f9acf8d93afc3.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh8pjaHJcy1wgT3n3h2tbL8SqTUvYj_4y6AfXz9xPklVAc6Bf4mpok61p0A25UIoNbBYiEopFoz5tBBnk560eUNDfB5O2r7pFjGxmmBMFJvG8ahj20GCFALusLZFrH75N3MaTmSD4TG01KD75ByddXJVIz7ZeVC7puQwBEH9GcpnF-qlrWjcVnLddz12bkv/s1912/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202026-03-04%20045700.png)  
*Блоґ котрий копіюється*

  

[![](/assets/images/blog/fdeb3cf495488d9e-69de39fe4772b621.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi9t2wb-DqamZzghZMXiw75UG3obQtS9aWHO1rJ-Vp1-QTBGALRYbHNztLJk6zMGPaXvKoVocc5Cw1vPpxZR6e0RzxZePbj3eTCSARMol6mUvsG8kXDQ9bt1gejqzlscJSjf-lyQZocc0Io3KYZPOy8rroV2tVCC1ocN4dfupCRKi2Yuy-z2d3l9YQAa-Rw/s1370/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202026-03-04%20045610.png)  
*Результат копіювання на GitHub pages*

  

#### Опис продукту

Це
[Python-утиліта,](https://github.com/lexxai/BLOG2GHP)
яка синхронізує пости з RSS-стрічки Blogger у репозиторій GitHub Pages /
Jekyll.

Вона автоматично:

* завантажує зображення,
* переписує внутрішні посилання,
* генерує коректно оформлені Markdown-файли з YAML frontmatter.

### Можливості

* **Automated Sync**  
  Отримує пости з RSS-стрічки Blogger та
  синхронізує їх із репозиторієм.
* **Image Handling**  
  Завантажує зображення блогу та зберігає їх
  локально в:  
  assets/images/blog/
* **Link Rewriting**  
  Оновлює старі доменні посилання, замінюючи їх
  на нову адресу GitHub Pages.
* **Jekyll-compatible Outpu**t  
  Генерує Markdown-файли з правильно
  оформленим YAML frontmatter, сумісним із Jekyll.
* **Dry Run Mode**  
  Дозволяє попередньо переглянути зміни без
  фактичного створення або перезапису файлів.
* **Configurable**  
  Гнучка конфігурація через змінні середовища.

#### Як це працює

Утиліта працює як простий пайплайн обробки даних:

### Отримання RSS

Скрипт підключається до RSS-стрічки Blogger та отримує список постів.

З RSS витягується:

* заголовок
* дата публікації
* HTML-контент
* категорії (labels)
* постійне посилання (permalink)

### Обробка HTML-контенту

Отриманий HTML проходить декілька етапів трансформації:

* очищення зайвих тегів
* конвертація HTML → Markdown
* переписування внутрішніх посилань на новий GitHub Pages домен
* пошук усіх зображень

### Завантаження зображень

Усі знайдені зображення:

* завантажуються локально
* зберігаються у assets/images/blog/
* перейменовуються за безпечним шаблоном
* їхні шляхи автоматично оновлюються в Markdown-файлі
* Таким чином блог стає повністю автономним і не залежить від
  зовнішнього CDN Blogger.

### Генерація Markdown-файлу

Для кожного поста створюється файл у форматі: YYYY-MM-DD-post-slug.md

Файл містить:

* YAML frontmatter (title, date, tags, layout)
* основний Markdown-контент
* оновлені локальні шляхи до зображень
* Формат повністю сумісний з Jekyll.

### Dry Run режим

У режимі dry-run:

* файли не записуються
* зображення не зберігаються
* показується список змін, які будуть виконані

Це дозволяє безпечно перевірити результат перед реальною синхронізацією.

### Інтеграція з GitHub Actions

Скрипт можна запускати:

* локально
* через cron
* автоматично через GitHub Actions

У випадку GitHub Actions:

* Виконується запуск утиліти
* Генеруються / оновлюються Markdown-файли
* Зміни комітяться у репозиторій
* GitHub Pages автоматично перебудовує сайт

У результаті отримуємо повністю автоматизований процес резервного
копіювання та публікації.

[![](/assets/images/blog/70eebaceef240f01-30a8870e9f3badaf.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgMbdjFyri5Wvn8WIpyuiJoLhyphenhyphenUVxBxtl_r6e-m_D7UFgjeRHfvMSuVd80Ii-82T5XeDKIIr9NXQf9l1yitvAhAGtrxLnxRAeQ9oMTvLxXEZ7Qi29paUyiGNpcJdnO_wmeAkfSjAo6GtuHQ260DbJWLV76Li_ao8xfIB32oqsQ8goZi97C1CQZhD6uvS1Fc/s1883/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202026-03-04%20202020.png)  
*GitHub Actions*

### Підсумок процесу

RSS → HTML → Markdown → Download Images → Rewrite Links → Commit → GitHub
Pages Build

#### Посилання

* [GitHub repository - BLOG2GHP](https://github.com/lexxai/BLOG2GHP)
* [Маємо те, що маємо. Усе що відбувається - на краще](/)
* <https://lexxai.github.io/>
