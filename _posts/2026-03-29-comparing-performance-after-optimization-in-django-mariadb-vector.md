---
layout: post
title: "Comparing Performance After Optimization in django-mariadb-vector"
date: 2026-03-29 18:28:00 +0000
tags: ["Django", "django-mariadb-vector", "JSON", "mariadb", "mysql", "orjson", "performance", "SQL"]
blogger_orig_link: https://lexxai.blogspot.com/2026/03/comparing-performance-after.html
---

Since version v0.2.0, the '[django-mariadb-vector](https://github.com/lexxai/django-mariadb-vector)' library includes several optimization options. Here are the results from performance tests.

[![](/assets/images/blog/ba2a47ea6962ae43-65e1f6352affc05e.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj-rmEfoDsCN-hz_Dnqy3oOhZf-ZWtnr4LLMXwi4tiW4e2qIJsu9cKMDrR99GPgoPQ4xmgTyFLHzJZftZW0EEgJRYttBhCnZhC7S0dVecu7M1qWaXSRrPNEfLl5YOw7qCrkpbF-TiEX34oGMBYERQeUJk-vFy1TE_1g3woGrfO9vr4QiNN2tirD_4wTQBE1/s1690/.perf_v0.2.0.png)  
*Performance results*

***Note**: Performance was measured using 20,000 iterations (3 runs) in `tests/test\_performance.py`, with randomly generated vectors of dimension 3072.*

### Benefits 'orlson' vs 'json'

* Up to **~20×** **faster** compared to the standard `json` library on generate vector data
* Up to **~8×** **faster** compared to the standard `json` library on response vector data

### Benefits of 'binary' on response vector data

* Up to **~16× faster** compared to the standard `json` library
* About **~2× faster** compared to `orjson`

### Testing output

```
Warming for 3 seconds

decode_binary start testing...
binary: 1.8913245666384075 [1.9641265999525785, 1.8629208999918774, 1.846926199970767]

decode_orjson_str start testing...
json_str: 3.804304266697727 [4.095871299970895, 3.7040354000637308, 3.6130061000585556]

decode_json_str start testing...
json_str: 30.897333099972457 [30.78853879997041, 31.119306599954143, 30.784153899992816]
* decode binary is fastest than json in 16.34 times
* decode binary is fastest than orjson in 2.01 times
* decode orjson is fastest than json in 8.12 times

encode_orjson_str start testing...
orjson_str: 2.32788303331472 [2.455615399987437, 2.2925777999917045, 2.235455899965018]

encode_json_str start testing...
json_str: 51.04346506666237 [50.961705199908465, 50.88195970002562, 51.28673030005302]
* encode orjson is fastest than json in 21.93 times
```

### Reference

🔗 [DEMO Django application of usage library 'django-mariadb-vector' (MariaDB Vector)](/2026-03-28-demo-django-application-of-usage-library-django-mariadb-vector-mariadb-vector.md)

🔗 [Django MariaDB Vector package on pypi - 'django-mariadb-vector'](/2026-03-28-django-mariadb-vector-package-on-pypi---django-mariadb-vector.md)

🔗 Repo library: <https://github.com/lexxai/django-mariadb-vector>

🔗 Repo Demo: <https://github.com/lexxai/django-mariadb-vector-demo>
