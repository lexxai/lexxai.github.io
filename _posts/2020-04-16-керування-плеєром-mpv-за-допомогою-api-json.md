---
layout: post
title: "Керування плеєром MPV за допомогою API JSON"
date: 2020-04-16 23:57:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2020/04/mpv-api-json.html
---

[![](/assets/images/blog/6282fe5c150f63b0-07c90f7139a58e25.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj8Wp445OpXo7mTPDzhMeWjvicQCOE_Y9SgYp2LagF4FhOteRcFUs3IA2sOj4Pa_bqlN-QoxIYEHsWMX7DG0slk3u-dXKzXhwNSPkFYK7_E5SV72AfRcC9v0Y42KFl9kXaCoNdBnB7_yDh1/s1600/mpv-logo-128.png)

Є багатоплатформовий відео плеєр з відкрити кодом - [MPV](https://mpv.io/).  
Все що він може і як цим керувати описано в [офіційній документації](https://mpv.io/manual/stable).  
Є задача - керувати цим плеєром коли він запушений на віддаленому екрані.  
Один з варіантів керування це використати [JSON-based IPC протокол](https://mpv.io/manual/stable/#json-ipc).  
У документації є приклади, тому на їх основі запускаю плеєр в ОС Ubuntu.  
  


#### Початковий запуск плеера

```
mpv --idle --loop-playlist --fullscreen --geometry=800x600 --input-ipc-server=/tmp/mpvsocket
```

В такому режимі плеєр загущений у режимі очкування команд, без активації інтерфейсу.  
Інший варіант це запустити з статичним зображенням, наприклад логотипом, з нескінченим часом показу .  

```
mpv LOGO.PNG --loop-playlist --image-display-duration=inf --alpha=no --fullscreen --geometry=800x600 --input-ipc-server=/tmp/mpvsocket
```

  

[![](/assets/images/blog/34586105248326fe-7b6ddf7d4489c89f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhOjsffrzrWtzYbdFfmeK97xmkSWqsAcx8D8AvTQwQ93qNcEFgshEuVKiPv2dElXgRbT3aiWrMF59Eb_2aJhKwuZsjoVp1dzqPXlFWElYnH6gJ-y8od0WYIrCVFuzqwPejt52neNRWyEJ9B/s1600/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D0%25B0+%25D0%25B7+2020-04-16+23-39-06.png)  
*Плеєр MPV в режимі очікування*

#### Керування плеєром

Для керування плеєром буду використовувати простий скрипт сценарію [BASH](https://uk.wikipedia.org/wiki/Bash), для з'єднання через [SOCKET](https://uk.wikipedia.org/wiki/%D0%A1%D0%BE%D0%BA%D0%B5%D1%82) використовую програму [socat](https://en.wikipedia.org/wiki/Netcat#Ports_and_reimplementations).  

```
sudo apt install socat
```

Простий варіант послати команду до плеєра це:  

```
echo ${COMMAND} | socat - /tmp/mpvsocket
```

```
COMMAND='{ "command": ["get_property", "playback-time"] }' 
echo ${COMMAND} | socat - /tmp/mpvsocket
```

Результат буде в JSON форматі:
  

```
{"data":190.482000,"error":"success"}
```

Тому для обробки відповіді в форматі JSON встановлюю програму jq.
  

```
sudo apt install jq
```

Варіант з функцією багаторазового використання з обробкою результату:  

```
function cmd()
{
 res=$(echo ${1} | socat - /tmp/mpvsocket)
 error=$(echo "${res}" | jq -r '.error')
 if [ "${error}" == "success" ] ;then
  data=$(echo "${res}" | jq -r '.data')
  echo "${data}"
 fi
}
```

Тпер запит буде виглядати так:
  

```
cmd '{ "command": ["get_property", "playback-time"] }'
```

Відповідь буде:
  

```
190.482000
```

#### Встановлення playlist для початку програвання

Перелік файлів котрі потрібно програвати зазвичай формується у файл "playlist.m3u".  
Для передачі команди плеєра:  

```
PLAYLISTFILE=playlist.m3u
cmd "{ \"command\": [\"loadlist\", \"${PLAYLISTFILE}\",\"replace\"] }"
```

#### Додавання файлу до списку програвання

```
URLMEDIA=somemedia.mp4
cmd '{ "command": ["loadfile","'${URLMEDIA}'","append-play"] }'
```

#### Додавання файлу до списку програвання з власними параметрами відеофільтрів

Наприклад, відео "somemedia.mp4" має довжину 40 сек.
Додаю фільтр fade, де є параметр початку - IN, та  на завершенні - OUT. Так на початку відео ефект FADE-IN в позиції 0 сек, довжина 1 сек. Завершується відео ефект FADE-OUT в позиції 39 сек, довжина 1 сек.
  

```
cmd '{ "command": ["loadfile","'${URLMEDIA}'","append-play", "vf-set=\"fade=t=in:d=1\",vf-add=\"fade=out:st=39:d=1\""] }' 
```

  
Важлива примітка.  У пердачі параметрів фільтра "vf-set=\"fade=t=in:d=1\""  передавати у екранованно .  
  

#### Також можна прослуховувати значення стану плеєра

```
while IFS= read -r line
do
 event=$(echo "$line" | jq -r '.event')
 case "${event}" in
   'end-file')
      echo "${event}"
      echo playlist-playing-pos $(cmd '{ "command": ["get_property","playlist-playing-pos"] }')
   ;;
   'file-loaded')
       echo "${event}"
       echo "FN:" $(cmd '{ "command": ["get_property", "filename"] }')
   ;;
   *)
       echo "${event}"
   ;;
 esac
done < <(( socat - /tmp/mpvsocket ))
```

  
і таким чином реагувати на стан плеєра і робити власні команди.
