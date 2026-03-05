---
layout: post
title: "Pleroma - change to new domain directly on database postgress"
date: 2021-09-10 19:53:00 +0000
tags: ["database", "pleroma", "postgress", "social"]
blogger_orig_link: https://lexxai.blogspot.com/2021/09/pleroma-change-to-new-domain-directly.html
---

[Pleroma](https://pleroma.social/) - is social networking software compatible with other Fediverse software such as
[Mastodon](https://joinmastodon.org/),
[Misskey](https://join.misskey.page/en/),
[Pixelfed](https://pixelfed.org/) and many
[others](https://fediverse.party/).

Якщо було змінено основний домен то змінити усі записи що існують на новий домен зі старого домену можна за допомогою цієї послідовності SQL обробок у базі даних postgress.
Для засовування замінити OLD.DOMAIN, та NEW.DOMAIN, реальними значеннями у редакторі.

```
sudo -u postgres  psql -d pleroma -c "\
 ALTER TABLE ONLY public.chats \
    ALTER CONSTRAINT chats_recipient_fkey DEFERRABLE INITIALLY DEFERRED;

 BEGIN;
  UPDATE public.users SET ap_id = replace(ap_id, '://OLD.DOMAIN', '://NEW.DOMAIN');
  UPDATE public.chats SET recipient = replace(recipient, '://OLD.DOMAIN', '://NEW.DOMAIN');
 COMMIT;

 ALTER TABLE ONLY public.chats \
    ALTER CONSTRAINT chats_recipient_fkey NOT DEFERRABLE;

 UPDATE public.users SET avatar = replace(avatar::TEXT, '://OLD.DOMAIN', '://NEW.DOMAIN')::jsonb;
 UPDATE public.users SET follower_address = replace(follower_address, '://OLD.DOMAIN', '://NEW.DOMAIN');
 UPDATE public.users SET following_address = replace(following_address, '://OLD.DOMAIN', '://NEW.DOMAIN');
 UPDATE public.users SET banner = replace(banner::TEXT, '://OLD.DOMAIN', '://NEW.DOMAIN')::jsonb;
 UPDATE public.users SET bio = replace(bio, '://OLD.DOMAIN', '://NEW.DOMAIN');
 UPDATE public.activities SET data = replace(data::TEXT, '://OLD.DOMAIN', '://NEW.DOMAIN')::jsonb;
 UPDATE public.activities SET actor = replace(actor, '://OLD.DOMAIN', '://NEW.DOMAIN');
 UPDATE public.activities SET recipients = replace(recipients::TEXT, '://OLD.DOMAIN', '://NEW.DOMAIN')::varchar[]
 UPDATE public.apps SET redirect_uris = replace(redirect_uris, '://OLD.DOMAIN', '://NEW.DOMAIN');
 UPDATE public.apps SET website = replace(website, '://OLD.DOMAIN', '://NEW.DOMAIN');
 UPDATE public.instances SET host = replace(host, 'OLD.DOMAIN', 'NEW.DOMAIN');
 UPDATE public.instances SET favicon = replace(favicon, '://OLD.DOMAIN', '://NEW.DOMAIN');
 UPDATE public.lists SET following = replace(following::TEXT, '://OLD.DOMAIN', '://NEW.DOMAIN')::varchar[]
 UPDATE public.lists SET ap_id = replace(ap_id, '://OLD.DOMAIN', '://NEW.DOMAIN');
 UPDATE public.objects SET data = replace(data::TEXT, '://OLD.DOMAIN', '://NEW.DOMAIN')::jsonb;
"
```

актуально для  v2.3.0.
