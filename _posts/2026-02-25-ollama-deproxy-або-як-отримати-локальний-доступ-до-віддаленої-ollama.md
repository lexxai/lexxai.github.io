---
layout: post
title: "Ollama DeProxy або як отримати локальний доступ до віддаленої Ollama"
date: 2026-02-25 15:43:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2026/02/ollama-deproxy-ollama.html
---

[![](/assets/images/blog/5407e647a6bbf4da-382eaa1ca0d7ddde.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjWx_eS9agZ_zK8fMztc_ny0utU6bQbsF0Liq8wH1lGHpP5GUfeHKCk2gDkwhCWTaJgOfOXGSMr7ye-g2GZ5eMmJrkr9QILfqBNXIi-jWlRDZXJFYd3pmt_VC8puW5Df-4u72QE-fXngy3kwstWD4Rd6D2uENUxgi0iAjzJGNEfMByuAZOkc0adNFBJSBPZ/s1024/ollama-deproxy.png)  
*Ollama DeProxy*

Я маю віддалений сервер із GPU, на якому запущена Ollama.

Водночас середовище розробки зазвичай очікує, що Ollama доступна локально -
наприклад, за адресою *http://localhost:11434* або в межах локальної мережі
(*http://192.168.0.111:11434*).

### Класичні рішення

Найпростіший варіант — SSH-тунель:

```
ssh remote@server -L 11434:localhost:11434
```

Після цього локальний *localhost:11434* проксуватиметься на віддалений сервер.

Якщо ж розробників декілька і вони працюють з різних офісів або через інтернет
— можна використати VPN. Це теж робоче рішення, але воно потребує додаткової
інфраструктури та адміністрування.

### Проблема з авторизацією

У моєму випадку Ollama використовується разом із OpenWebUI, який проксіює
доступ до Ollama через власний API з токен-авторизацією.

Однак більшість застосунків, що інтегруються з Ollama, очікують простий доступ
до http://localhost:11434 без жодної авторизації. Через це вони не можуть
напряму працювати через OpenWebUI.

### Рішення — Ollama DeProxy

Щоб спростити інтеграцію, я написав невеликий застосунок -
**[Ollama DeProxy](ollama-deproxy)**.

Його ідея проста:

* локально він виглядає як звичайна Ollama (localhost:11434);
* всередині - проксіює запити на віддалений сервер;
* за потреби додає авторизацію;
* дозволяє централізовано керувати доступом.

Таким чином:

* розробникам не потрібно налаштовувати SSH-тунелі;
* не обов’язково піднімати VPN;
* клієнтські застосунки продовжують працювати так, ніби Ollama запущена
  локально.

У результаті віддалений GPU-сервер використовується прозоро, без змін у
клієнтському коді.

### Репозиторій:

 **Ollama DeProxy** -
<https://github.com/lexxai/ollama-deproxy>  
  

### Приклади використання:

[![](/assets/images/blog/a84001fe810b3e04-85765c91b2a6b9c7.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj4oDdZJ87OI0gMvKw3onyi0yLWFr6AZc04NbX8P5dxgDS4CKhQ_cf3L3fH84oSvW2fsBml7jZK_PTCexI0pnX514ouylZcMh7H2FnqRV2t5ENp_NmqBZPMur-bR_ss1ieqJQw2TfkTMOlWn11rtZ1_A-IyWO7SdpV7-U9TUbuE4RBnfm4Gj93jGOy0jRWg/s1837/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202026-02-25%20173628.png)  
*Docker container Ollama DeProxy*

[![](/assets/images/blog/43365cb74653e6c9-9052af3a16636e07.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgiZbRMugT6pORi5fe733spJ0gbvs6FdMAMPbnxMyj8INJ-Z2zEWGBC21CpsruocHBsq9P_yEHHgS042rC-ZdX8FZB39IpgQO7c3J-QTawWrWw3nFLi7ZLjz-x-f_uBzFlXDeM0Y71e2eUF6N4lylYo3IO2mkLPXkGHd8e6CCXn1js3uU12o-IHqmdT2YIg/s1919/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202026-02-25%20173507.png)  
*VS Code + Continue + Ollama DeProxy*

[![](/assets/images/blog/2407ff4104a8caea-d33ef03cc0af9c6d.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgo1GjkMyRMlsgtC3tPciXqIp6zuL9Jx0M6UoqcfOsYkb_bBhQHXrhQ35xSJ0WnVwagp6lvQnkWsEqPAlmQdMwTkN7xxWA0CJD-zcLdGqGlJCxaKADv1NlJvALhFOXovWOuoJJmck1RXpiLy-OHgc-sVSlq_nrfCDR6fV7P7yKVVA78oNHXSo8rfOj8owPZ/s1919/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202026-02-27%20143139.png)  
*VS Code + Copilot GitHub (Old version) + Ollama DeProxy*

[![](/assets/images/blog/536964798ab06d95-2ee020613f9dad9c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhLdl8Lte8XIQTdbwtduu5FhWhFSdmzPtperOquDj3qAwtrDhjW10MKFQavHvk_hA6tfNBIXYfJPi57Hmd_8t_CaQpV-JPNF7s39J2lB5WF9NeszZUsYfyV8KMV-fvP5WHZ4mYGN-nUc4rcPGfX25gIhlzgbcWU9AGLl57ubFKDJqcmkQHGHZAzd1yN2kJN/s1915/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202026-02-28%20140322.png)  
*VS Code + Copilot GitHub (0.37.9) + Ollama DeProxy (CORRECT\_NUMBERED\_MODEL\_NAMES=True)*

[![](/assets/images/blog/3f799e76847ecd64-f83d3dce2a710871.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEg-wY4774AFVNWzLH_MkVK_Y6FI0y08_HRSoCI9g-rn8vWnrVrpCxmO1v4UH2jGzjcgcuyAt3oai9IiiWmCh2ffehlweWRD3HluuPrjBqFHFqyBsp6V84L9DM9lKl300k3WLyJwHq_7aBPPjJm80TYWVT9Ra1TNcVBRBH9hSD7_0HKn0K21YmiRz7VBQiOi/s1900/%D0%97%D0%BD%D1%96%D0%BC%D0%BE%D0%BA%20%D0%B5%D0%BA%D1%80%D0%B0%D0%BD%D0%B0%202026-03-05%20171748.png)  
*PyCharm  AI Assistant + Ollama + Ollama DeProxy*

### За матеріалами:

* [Оllama](https://github.com/ollama/ollama)
* [openwebui.com - api proxy](https://docs.openwebui.com/reference/api-endpoints#-ollama-api-proxy-support)
* [ollama-deproxy](https://github.com/lexxai/ollama-deproxy)
