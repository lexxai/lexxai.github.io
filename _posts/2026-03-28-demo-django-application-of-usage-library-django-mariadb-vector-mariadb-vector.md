---
layout: post
title: "DEMO Django application of usage library 'django-mariadb-vector' (MariaDB Vector)"
date: 2026-03-28 18:00:00 +0000
tags: ["Django", "mariadb", "mysql", "SQL", "vector", "vector-database"]
blogger_orig_link: https://lexxai.blogspot.com/2026/03/demo-django-application-of-usage.html
---

[![](/assets/images/blog/f56c645707d8edb6-49d29085051d37f5.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhWpdawnrLBWLhdvSLOBiAaLDTc0HrMioHcvvisuQ3w949jqqu12_efE-MpV1p5Nw2ndIS8vnF75rMj3MeRrASxjDVojhS8gf213_2uBtHhONEzxvknIN08yyXQofOj_7uT0IttLd3OTkj4q00NyOklsCr42X8y3tyyJQdX9tSzxqtYkh3uzHkLczvZ-TNr/s1316/django-mairadb-vector.png)  
*pip install django-mariadb-vector*

  
📒 Django MariaDB Vector DEMO application   

A minimal demo project showing how to build article recommendations using
vector similarity in Django with MariaDB as the database.

The app stores articles, embeds their content into vectors, and then finds
similar articles based on vector distance.

  
🔗 Repo: <https://github.com/lexxai/django-mariadb-vector-demo>  
🔗 Examples: <https://github.com/lexxai/django-mariadb-vector-demo/tree/main/docs>

🔗 Repo library: <https://github.com/lexxai/django-mariadb-vector>  

### Features

* Django application using MariaDB as the primary database
* Article model with text content
* Vector-based similarity search for recommendations
* Simple UI:
* List of all articles

+ "Similar articles" view for a selected article
+ Admin interface to add and manage articles

### Features Demonstrated:

* MariaDBVectorField: Storing vector embeddings as a specialized field
  in Django models.
* MariaDBVectorIndex: Creating HNSW (Hierarchical Navigable Small World)
  indexes for efficient similarity search.
* RecommendationManager: Using a custom manager to perform similar\_to()
  queries based on vector similarity.

### Examples

[![](/assets/images/blog/25b29e0b4e0ce045-bb675b88b82bbcce.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh6H2DxIFg2u5vuWgAY-dNk6NsTWgbTyUo7JciV4NJp7rSpG1sE_h2oK5clBgTLgMxErBrE2dwcCzAzVNf29zxI7ht6zEfpS38_NS2fPVhQdwjjadOUYQYPwLMqUtcAEf4oFM-bfow4KM_DB-8coNFVHUk24iHQnxMUPlB8uJw1IPZDQgVuSXUtY4tELRMH/s1391/arts.png)  
*List of all articles*

  

[![](/assets/images/blog/e4cfd272b9ebd7a8-0c81c95b508da644.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEh6eIEhQU-P5yneae_M0iLsOS9jEpU2PG19YLH_y8C4TkDZETf7hTgSK_L9YSpsi4nbWt21obz_QkIxrHKpYn32VPOXc0b7YxtS-Hyf5jKNWSdiEcxKa__GuoM2bD-aeKrL8xiRg4soh5CsAGAPdt6QH4fs6r8VYP8EYL-uC2x0oiPqCtSze5XDmX6KKCVu/s1430/art1.png)  
*List of articles similar to Article with pk=1*

  

[![](/assets/images/blog/8e4c7d4e0af9b20b-2a241e4e0cab5ce6.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiBod4VbvvLAAbe8jid7mFtHAlqaXVswVRRSV7-GqIZUMVkcsGtzozMtZAdzQ0RtYOCE1Hnh6pnpvDKY1q-KXebyr50-Jn0Jf-Tm9erc3UD7B8ruc_ndTwBr9KAlqp4ELuLbCsP_FD0CqtxYVjwPFQhUJeI8i3Cpz27PBxrs4gVzAVtjJhbFuEHcfBVWCMi/s1899/admin-art2.png)  
*Adding new article data through the Django admin*

  

### Simple example of usage:

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

### Using a Recommendation Manager can simplify vector searches in your application:

*models.py:*

```
from django.db import models

from django_mariadb_vector import MariaDBVectorField, MariaDBVectorIndex
from django_mariadb_vector.managers import RecommendationManager


class MyModel(models.Model):
    embedding = MariaDBVectorField(dimensions=3)
    
    objects = RecommendationManager(vector_field="embedding")
    
    class Meta:
        indexes = [
            # Vector index (MariaDB 11.8.2+)
            MariaDBVectorIndex(fields=["embedding"], dimensions=3, m=16),
        ]
```

### reference vector:

```
from .models import MyModel

reference_vector:list[float] = [0.1, 0.2, 0.3]

# Find 5 most similar records to a reference vector
results = MyModel.objects.similar_to_vector(reference_vector, limit=5)

for item in results:
    print(f"{item.name} - Distance: {item.distance}")
```

### reference id:

```
from .models import MyModel

reference_id:int = 1
# Find 5 most similar records to a reference object by id
results = MyModel.objects.similar_to(reference_id, limit=5)

for item in results:
    print(f"{item.name} - Distance: {item.distance}")
```

#Django #MairaDB #VectorDatabase #DjangoORM
