---
layout: post
title: "Django MariaDB Vector package on pypi - 'django-mariadb-vector'"
date: 2026-03-28 15:21:00 +0000
tags: ["Django", "mariadb", "vector", "vector-database"]
blogger_orig_link: https://lexxai.blogspot.com/2026/03/django-mariadb-vector-package-on-pypi.html
---

[![](/assets/images/blog/f56c645707d8edb6-49d29085051d37f5.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhWpdawnrLBWLhdvSLOBiAaLDTc0HrMioHcvvisuQ3w949jqqu12_efE-MpV1p5Nw2ndIS8vnF75rMj3MeRrASxjDVojhS8gf213_2uBtHhONEzxvknIN08yyXQofOj_7uT0IttLd3OTkj4q00NyOklsCr42X8y3tyyJQdX9tSzxqtYkh3uzHkLczvZ-TNr/s1316/django-mairadb-vector.png)  
*pip install django-mariadb-vector*

  
📒 Django MariaDB Vector  

The Vector field, introduced in
[MariaDB](https://www.linkedin.com/company/mariadb/) 11.7, now has a
simple library called 'django-mariadb-vector' that adds Django ORM support for
it.   
  
Created by me and shared for everyone to use.  
  
🔸
[pip install django-mariadb-vector](https://pypi.org/project/django-mariadb-vector/)  
  
🔗 Repo:
<https://github.com/lexxai/django-mariadb-vector>  
🔗 Demo:
<https://github.com/lexxai/django-mariadb-vector-demo>  
🔗 Examples:
<https://github.com/lexxai/django-mariadb-vector-demo/tree/main/docs>  
  
MariaDB introduced native vector support, allowing you to store
embeddings and perform similarity search directly in the database.  
  
However,
Django currently lacks:  

* a native VECTOR model field
* ORM support for vector queries
* automatic migration
* support for vector indexes

This project fills that gap by providing a clean, Django-native
way to work with MariaDB vectors.  
  
Simple example of usage:  

```
!pip install django-mariadb-vector

from django.db import models
from django_mariadb_vector import MariaDBVectorField, MariaDBVectorIndex, VecDistance


class MyModel(models.Model):
    embedding = MariaDBVectorField(dimensions=3)

    class Meta:
        indexes = [
            # Vector index (MariaDB 11.8.2+)
            MariaDBVectorIndex(fields=["embedding"], dimensions=3)
        ]

# Find 5 most similar records to a reference vector
reference_vector = [0.1, 0.2, 0.3]
results = MyModel.objects.annotate(
    distance=VecDistance("embedding", reference_vector)
).order_by("distance")[:5]
```

#Django
#MairaDB
#VectorDatabase
#DjangoORM
