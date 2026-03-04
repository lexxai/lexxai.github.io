---
layout: post
title: "Засіб перегляду Torrent-TV - з використанням адаптованої версії плеєра \"Simple TV\" від Maxwell"
date: 2013-02-24 14:09:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2013/02/torrent-tv-simple-tv-maxwell.html
---

![](http://www.air.io/wp-content/uploads/Torrent-TV.jpg)

  
А ще цікавий засіб перегляду Torrent-TV - з використанням [адаптованої версії](http://mytalks.ru/index.php?topic=6.msg6#msg6) плеєра "[Simple TV](http://iptv.gen12.net/dokuwiki/doku.php/mantis/simpletv/start)" від [Maxwell](http://mytalks.ru/index.php?action=profile;u=10)- [Перегляд торрент-тв через Simple-tv](http://mytalks.ru/index.php?topic=6.0).  

За допомогою цієї програми можна перетворити простий нетбук у медіа плеєр і підключити його до телевізора. А якщо ви-користувати можливості програми у роботі з пультом дистанційного керування, або бездротової клавіатури то можна використовувати  у якості  повноцінного медіа плеєру та інтернет браузеру.

[![](/assets/images/blog/ce05fadd01b0a961-7dff8d3ce8bd37a0.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhf8zOHSQ4Up15oGMfKuHT2znqFCmb3Vt70rzJUp7GKUNNpWSBJQLYMBwm9RSF6BC1JRmOSu_4sMvSMiq2jaUpL31Ny5FRvm8UrN5vDn8Xthh5cUH8NVd8CZ7ISh4frv1vx9P_c2nflLdi0/s1600/IMG_8841.JPG)  
*"Simple TV" від Maxwell на телевізорі з каналами Torrent-TV*

Можна до Simple TV завантажит IPTV плей лист локального кабельного оператора, і дивитись його канали, наприклад "[Triolan TV](http://www.triolan.tv/)", тим паче оплачувати кодовані можна канали тільки використовуючи IPTV, навіть з подобовою сплатою за окремий і потрібний канал.

[![](/assets/images/blog/fd4c5a85038f876d-0e114d72e86d7904.jpg)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjWvj3jeTNDS3aSRVo7_PoZDBa80b4TDqmUkQvKdzpOkVnapsu3saFHxhsI8gGh3MdshiCL-QbwmfnP-UbXU1J9-We0ea_9592yKREeM3kuxXdhzONPAwCyW0wQ1X_TwukdPu4ETR7bXEnT/s1600/IMG_8843.JPG)  
*"Simple TV" від Maxwell на телевізорі з каналами IPTV Ttriolan-TV*

  
Якщо треба вивести додатково трансляцію у TCP потік то

шукаємо файл vlcrc (у цій теці %AppData%\SimpleTV V03\) та додаємо рядок  
--sout=#duplicate{dst=std{access=http,mux=ts,dst=:8902},dst=display}.

Після цього можемо підключатися до TCP потоку за адресою IP:8902. ([взято тут](https://vk.com/page-49849471_44358520)).  
Але, цей метод у мене не працює, але якщо використати файл vlcrc що знаходиться у теці "Work" з плеєром ("%Program Files(x86)%\SimpleTV by Maxwell\Work\"), то працює.  

[![Перше торрент телебачення](/assets/images/blog/766f42c008e2ddb4-13be3bafed80e1f9.jpg)](http://torrent-tv.ru/)
