---
layout: post
title: "Модифікація кнопки для дзвінка на кнопку з датчиком руху та індикацією стану розумного будинку  на базі SONOFF/ESP/Tasmota"
date: 2022-01-09 22:41:00 +0000
tags: ["button", "ESP", "motion", "PIR", "sensor", "TASMOTA", "датчик", "розумний дім"]
blogger_orig_link: https://lexxai.blogspot.com/2022/01/sonoffesptasmota.html
---

Задача на холодильник прикріпити магнітом кнопку - датчик.  
Датчик руху дозволить ввімкнути освітлення і наступній кімнаті перед виходом з кухні.  
А механічна кнопка, дозволить керувати розумним реле у кухні в ручному режимі на випадок не робочого стану логіки контролера розумного будинку.

В якості датчику руху використано мініатюрний модуль SR602.

[![](/assets/images/blog/8321bd847cb4b411-397e4601b4c38782.jpg)](https://blogger.googleusercontent.com/img/a/AVvXsEiuwiCRL3fkQATKmVWW7MancHULbliIzJmYLa4Q_RbziBUGZd67Xf_tR-oT0ZTPHPorYsHUQJgSRkvNaazN6eEqHOdMdujtyc2PhdjgH-s7cTHCu5uf2d0HcO92Db8Q78bYJ50yeu7xl9GiswUdCZbB5A-1hovztcAIn0y43Nunk6z5YjeK36AUjZImqQ=s640)

  
 До кнопки додано магніт.[![](/assets/images/blog/8321bd847cb4b411-b0724435620dd72c.jpg)](https://blogger.googleusercontent.com/img/a/AVvXsEj59_OYaDUDeM3_BHzJgjpVwB-Y8ygKo-N52t3DUk7hPkltssmmYgpM7p6NtFyC8K8XTqtVC2wc-fF3xGPpfz8EyRPQqfglkeLAEK9ZEadY_x-rlZmQiFURU0l7M_EGVJvNUJcgozH3tlm6r22lJQCnZKYQikd6iCllYqwZSXrEJ3KGGlbH6L-_5MSm5g=s2261)

[![](/assets/images/blog/8321bd847cb4b411-af8f601491b60ff3.jpg)](https://blogger.googleusercontent.com/img/a/AVvXsEhIjMe5acZaQfFBgi1hbJfK9PkoSBnn7z8bdRvOh3_6n8YArtZkdlE0oHDSyrYz3CLGsP9yi709WbqVkETyRusOqFS5JobCHMbPDddmHr7qt9lRWefQyfJZgIBm6rkDQE5J1L_kMAOrBMXR6AcYa__pPyt0GjFN06f75PMAJrEtXTG0EBGhP4aaaT1l6w=s2803)

[![](/assets/images/blog/8321bd847cb4b411-88fcd0a1499e1b57.jpg)](https://blogger.googleusercontent.com/img/a/AVvXsEie08wzrTxszms9lj3h38YhZT4WJo8j1lVVKgn_w2AxfY1qaXth5BSxDCVA-4n3kYkKcDUhatpl2PLM9N0f5kmdQN_0Rr27Y1c-BkxqhiZDDW1_cBiRL_1xeG_9Zt0UoqgDttSE7YBBmyJZgV1JXiEKYXVuuA7yBtBnpS5tdIBGfwrYIdBwTSnR2oJMmA=s2425)

[![](/assets/images/blog/8321bd847cb4b411-9963197fbb326631.jpg)](https://blogger.googleusercontent.com/img/a/AVvXsEgcQbuB_Ry0n2mMpbkJJ8zLUqkxWE1pOVp-LtcCkQfEbqsiLQa4xnaKwElAsHvfRQQl4o_dS67wacmG_mlGYRPC7uOMjCqPrT9AghEdPBbKtquHwDop2zeHz22paJt-9rP3EdAEWX441Kxv_JT5uNIRKql6uvEa1PaZWJFDHZWsKDVPe9E8-YISxcVvag=s2718)

[![](/assets/images/blog/8321bd847cb4b411-a0b95f285fd1e2fa.jpg)](https://blogger.googleusercontent.com/img/a/AVvXsEjhFDLY_KwlsLWhiDJDu5ttb9TuuTtwqn6vKg66s6CXaiB7U9YiQjBR-Xf01xN1CXv480HM5te-CowFcDM_Yo1th79cpzLJyEgfW_5UIkZ6NP1Qjxjh-JbflBya5LqSEh9pXhzgrabCtLsR5SSo4MD7YELVg8hs6njWVtExrazfG1DCXMY3HBNaVpCNlQ=s2294)

[![](/assets/images/blog/8321bd847cb4b411-0585e2c7d74e7063.jpg)](https://blogger.googleusercontent.com/img/a/AVvXsEiake28dfTxq14WyDZBOJqZD3nNA8KFzQqfOoI1c_qeVgFWuyIo_AuhCsRgK2dN2N4Zyo7YzYAg_8gAXJgITinaL0023li5Bxy_Vxc2RKrEHJbZ46x86fGvSzMCpo1DcTVG8xJU9r0MxC7N5hW2gtGNy5XfeMdygSeTDsgHMpGBUZ6w9yTvbqNCeIY0yA=s2452)
