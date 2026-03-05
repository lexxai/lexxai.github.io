---
layout: post
title: "Оновлення IPTV playlist у OpenWrt для xupnpd"
date: 2013-12-22 01:28:00 +0000
tags: ["DLNA", "internet", "iptv", "multimedia", "open-wrt", "openwrt", "player", "script", "tv"]
blogger_orig_link: https://lexxai.blogspot.com/2013/12/iptv-playlist-openwrt-xupnpd.html
---

Цей скрипт використовується у роутері з прошивкою OpwnWRT для періодичного оновлення IPTV playlist кабельного оператора, у данному випадку TRIOLAN.  
  
/etc/download-playlist/dplaylist-triolan.sh:  

```
#!/bin/sh
cd /etc/download-playlist
mv playlist.m3u playlist.old
wget -q  http://goodnet.tv/playlists/kharkov/playlist.m3u
if [  -e "playlist.m3u" ]
then
    echo "loaded"
else
    echo "can't load, return old"
    mv playlist.old playlist.m3u
fi
```

  
Створюю посилання на завантажений файл до теки з плейлистами програми xupnpd.  
  

```
ln -s /etc/download-playlist/playlist.m3u /etc/xupnpd-rc/playlists
```

  
Даю завдання оновлювати плейлисти раз на добу програмою crond:  
/etc/crontabs/root:  

```
0 0 * * * /etc/download-playlist/dplaylist-triolan.sh > /dev/null
```

  
Налаоштовую xupnpd для оновлення та використання плейлиста playlist.m3u.  
/etc/xupnpd-rc/xupnpd.lua :  

```
cfg.playlists_update_interval=86400
playlist=
{
      { './playlists/playlist.m3u','Triolan TV'}

}
```

```

```

```

```

p.s. playlist  <http://triolan.tv/getPlaylist.ashx>
