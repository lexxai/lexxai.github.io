---
layout: post
title: "Нотатка. sociallogin of nextcloud use authentication via oauth2 from mastodon"
date: 2021-03-03 01:20:00 +0000
tags: ["mastodon", "nextcloud", "oauth2
sociallogin"]
blogger_orig_link: https://lexxai.blogspot.com/2021/03/sociallogin-of-nextcloud-use.html
---

Нотатка.  
Задача - авторизуватися в nextcloud з використанням бази даних користувачівmastodon.

#### 

#### [mastodon](https://uk.wikipedia.org/wiki/Mastodon_(%D0%BF%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D0%BD%D0%B5_%D0%B7%D0%B0%D0%B1%D0%B5%D0%B7%D0%BF%D0%B5%D1%87%D0%B5%D0%BD%D0%BD%D1%8F))

Server mastodon - https://mastodon.lexxai.pp.ua

New Applications - https://mastodon.lexxai.pp.ua/settings/applications/new

Name: *nextcloud*

Application website: *https://cloud.lexxai.pp.ua*  
Redirect URI: *https://cloud.lexxai.pp.ua/apps/sociallogin/custom\_oauth2/mastodon*  
Scopes: *read:accounts*

  

[![](/assets/images/blog/208d2cb1ad10704c-6f16dc0020bb86c4.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi9eEE8oR1HKPocIUNK8N_kRfpmHmlbKyCZb1bhkNdKjdUrNSF4VI1WZiVWSxxn7T7TLRPxDpu4PZLxGDMu-YOlPUH4nJWxIkQSuhfhK3z1yJTqBG2TbSNT5UxwiQnIMstsbnF0QJVEbUa8/s1114/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2021-03-03+01-03-50.png)  
*mastodon - new application oauth2*

[![](/assets/images/blog/b3d439f855a9fb4f-c68675bfa734cc27.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgKm7rqd2b1vKuQP0SuXec8fifdCsmnr03YPuGbDpppwo_y9ZRi8LnmIXW4QO2Qx3S5Nz2SPRLRO6foKka_2D7LIqnbNDiTu7t2Q2M5m8TBsNf6GSJWg1i7Rx9DA4oRdta54Dy1i2YPTZi_/s1103/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2021-03-03+01-05-06.png)  
*mastodon - application key/secret*

  
Client key 28q0bbVKEH44wzU0RCsHwypz9b3P48ONPcnCh2rpOnA  
Client secret 3Nehr1vv\_tmhQBxLPeN05NWRyHQ-YuZGqxYmzeTwi30

#### [Nextcloud](https://draft.blogger.com/#)

Server Nextcloud - https://cloud.lexxai.pp.ua  

Administration - Social Login https://cloud.lexxai.pp.ua/settings/admin/sociallogin  

#### Custom [OAuth](https://uk.wikipedia.org/wiki/OAuth)2 +

Internal name: *mastodon*  
Title: *mastodon*

*API Base URL:* http://mastodon.lexxai.pp.ua  
Authorize url (can be relative to base URL): *https://mastodon.lexxai.pp.ua/oauth/authorize*

Token url (can be relative to base URL): *https://mastodon.lexxai.pp.ua/oauth/token*  
Profile url (can be relative to base URL): *https://mastodon.lexxai.pp.ua/api/v1/accounts/verify\_credentials*  
Logout URL (optional): *https://cloud.lexxai.pp.ua/login?redirect\_url=/apps/dashboard/*

Client Id:  *28q0bbVKEH44wzU0RCsHwypz9b3P48ONPcnCh2rpOnA*  
Client Secret: *3Nehr1vv\_tmhQBxLPeN05NWRyHQ-YuZGqxYmzeTwi30*

Scope: *read:accounts*

Default group: *users*

## *NextCloud Custom OAuth2*

[![](/assets/images/blog/f54003e3dae7c87c-f35578b63741887c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEjZhaiFtwece6wSAKVDzcfTUADIdAqleELyx4tvrinxxFg3A7UHpaVXaUsi0mntDjUKLD5nKuHViZ5puRgCjNCMLpbfbmhhR6v1XSfJDGHDgF6_kn4IRNjqXzKks8kywPBGiwkQt1JTryWT/s509/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2021-03-03+03-08-52.png)  
*update user profile every login*

  

## *Nextcloud with Log In with mastodon*

#### Add username to netxcloud from mastodon:

 nextcloud/apps/sociallogin/lib/Provider/CustomOAuth2.php

...  
        $userProfile = new User\Profile();  
        $dataArray=$data->toArray();  
        $userProfile->displayName = $dataArray['username'];  
...
