---
layout: post
title: "Використання Google Cloud IoT MQTT  з консольного BASH сценарію та Mosquitto"
date: 2021-01-17 21:54:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2021/01/google-cloud-iot-mqtt-bash-mosquitto.html
---

[Google Cloud Platform](ttps://console.cloud.google.com) надає можливість використати [IoT Core](https://cloud.google.com/iot/docs) для підключення [IoT](https://uk.wikipedia.org/wiki/%D0%86%D0%BD%D1%82%D0%B5%D1%80%D0%BD%D0%B5%D1%82_%D1%80%D0%B5%D1%87%D0%B5%D0%B9) пристроїв за протоколом [MQTT](https://uk.wikipedia.org/wiki/MQTT) та [HTTP](https://uk.wikipedia.org/wiki/HTTP).  
Приклади налаштування, і основи роботи є на багатьох ресурсах і у відео:

* [Google Cloud IoT Core](https://youtu.be/76v16P-Wqe4)
* [Google Cloud IoT Core with Raspberry Pi](https://youtu.be/3Zwlj9x96Jg)

Моя задача створити безпечне підключення до [Google IoT Core](https://cloud.google.com/iot/docs) з консолі свого пристрою і використати мінімум програм:

* [bash](https://uk.wikipedia.org/wiki/Bash)
* [openssl](https://uk.wikipedia.org/wiki/OpenSSL)
* [mosquitto](https://mosquitto.org/)
* [jq](https://uk.wikipedia.org/wiki/Jq_(%D0%BC%D0%BE%D0%B2%D0%B0_%D0%BF%D1%80%D0%BE%D0%B3%D1%80%D0%B0%D0%BC%D1%83%D0%B2%D0%B0%D0%BD%D0%BD%D1%8F))

[![](/assets/images/blog/187d771851a050a5-a86817cf70d39aeb.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj00MDlmrK__cx2pccUbe9tGJzXTzsuJB0kDHNH5pd8ik3MgXrxX_nudCoFR6qJD5VE1kn995Y847KBjiGfKNT6d9QiqBr0aRNKPe1sQTbAaOHzdnJphvp8yX1v1gfPClh-630ZKYifZYjo/s1213/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2021-01-16+20-34-36.png)  
*Зв'язок mqtt topic (ts2) з google topic event (b01)*

[![](/assets/images/blog/502e3a45f42efff2-951d1d664bfa08db.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEj3X3p-DgFk2D0X9wDAHWLP4R95ep53RICK815tIX3saFyzLgEX0hhQEZsBi2sojr5NY9XrAHzS4ndqHaTGtNRSKGhMXPioYWeAFhKnR-HZIbGa6GHkbEGguHxVr9kydf64l2oaZnEhIitY/s1277/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2021-01-16+20-33-20.png)  
*Публікація до google topic event (b01) з mosquitto\_pub*

[![](/assets/images/blog/a4e13e899d6c667b-0f4352cdfd00a925.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgo1ZQRemZVeIqlBHnIWl0a_XtMWEduluzjwV41da9UJMExQRN4MLEemxuNxYPhe2LNz6iD5NfGmEBhkQAGkEug3-bksJo7AcFpWzlOo-TDenYD1VOK83scxuCvzHuQbzXSXh5VPmYg2BMU/s1213/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2021-01-16+20-33-54.png)  
*Перегляд отриманих повідомлень в google sub підписки на mqtt topic (ts2)*

[![](/assets/images/blog/61fa8f72ba2e9f71-d1657fd5212883cc.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiiyGjHR065tm3NpsrcQdXrG0ZUmAnvz6hrGH5J8YsFmj5ZIq68yUeNLZnlb3f0etg3866qPzyKWvBxR8-P7hEPMyykmvsh7P-_2h9hlSaJy1dxhWPo9FFakyEXneke24IQtjHiZ3w5lSXy/s1184/%25D0%2597%25D0%25BD%25D1%2596%25D0%25BC%25D0%25BE%25D0%25BA+%25D0%25B5%25D0%25BA%25D1%2580%25D0%25B0%25D0%25BD%25D1%2583+%25D0%25B7+2021-01-16+22-23-32.png)  
*Підписка до topic - error*

#### Скрипти для автоматизації підключення.

Головна особливість для зв'язку з Google Cloud Platform, те що потрібно автентифікувати пристрій котрий надсилає #MQTT повідомлення за допомогою [JWT](https://uk.wikipedia.org/wiki/JSON_Web_Token).

[![](/assets/images/blog/2b42acbedaea9bf2-26de368bb6795e2f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhFXUVwsQKJHa3tEmfIDprNokjRUXt8jLXPzw1B66IyesiMuq9ID0tbMzOPo7yAWiLkC26XFjZ9joC4RM5vgfeNSPqCYshYXhG9veGUeqyilBUz3u77qT18QQmjBcT4gCLNw3e5Ij9xFLU3/s376/auth-sequence2.png)  
*Автентифікація пристрою за JWT*

В мережі було знайдено скрипт який генерує [JWT](https://uk.wikipedia.org/wiki/JSON_Web_Token), який я трохи модифікував, і використовую. Для генерації [JWT](https://uk.wikipedia.org/wiki/JSON_Web_Token) необхідно попередньо [згенерувати публічний та особистий ключ](https://cloud.google.com/iot/docs/how-tos/credentials/keys#generating_an_rsa_key_with_a_self-signed_x509_certificate) та прив'язати його за інструкцією до Вашого пристрю (RS256\_X509): [Device security | Cloud IoT Core Documentation | Google Cloud](https://cloud.google.com/iot/docs/concepts/device-security?_ga=2.150977078.-1258548236.1592937774).

Вся інша методика описана в - [Publishing over the MQTT bridge | Cloud IoT Core Documentation](https://cloud.google.com/iot/docs/how-tos/mqtt-bridge).

[![](/assets/images/blog/bb545fc68adf3120-5ba9e4ecb8f03ce8.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEivDa_KOIDhXF0SSnVqu3raAc5OH8rIZ0m6lO4H4QpPGOQGzd-yGhYSm76Cw4ufajwW_SsEywywY68cvwHJ5unyoBd-mZt1-LPkLag38Mbafix__FHo25C-i2BkIbkyV9NA_6Rojd0J7l0g/s547/auth-sequence1.png)  
*Алгоритм створення JWT*

  

Для безпечного підключення до MQTT брокеру від Google треба мати файл центру сертифікації.

mqtt\_pub.sh:

```
#!/bin/bash

ProjectID="mqtt-lexxai"
LOCATIONS="europe-west1"
RegistryID="mqtt-lexxai"
DEVICE="wrt"
CLIENTID="projects/$ProjectID/locations/$LOCATIONS/registries/$RegistryID/devices/$DEVICE"
TOPICEVENT="/devices/${DEVICE}/events"
TOPICSTATUS="/devices/${DEVICE}/status"

function init_jwt() {
 ./jwt/generateJWTtoken.sh ${ProjectID}
}

function public() {
 echo "${CLIENTID}"
 mosquitto_pub -h mqtt.googleapis.com -p 8883 -d  \
 --cafile ./ca/roots.pem \
 --id "${CLIENTID}" \
 --username "unused" \
 --pw "$( cat ./jwt.txt )" \
  -q 1 -t "${1}" -m "${2}"
}

#MAIN

init_jwt

TOPIC="${TOPICEVENT}/b01"
MESSAGE="$( date )"

public ${TOPIC} ${MESSAGE}
```

generateJWTtoken.sh:

```
#! /usr/bin/env bash

RegistryID=$1
if [ -z "$RegistryID" ];then
 echo "Missed parameter about RegistryID"
 exit
fi

KEYFILE=./key/rsa_private.pem
OUTPUTFILE=jwt.txt

echo "Check OLD File JWT is ?"
if test `find "${OUTPUTFILE}" -mmin +20`
then
    echo "old enough, need generate a new"
else
    echo "not old enough, skip generate"
    exit
fi

# Static header fields.
HEADER='{
    "type": "JWT",
    "alg": "RS256"
}'

payload="{
    \"aud\": \"${RegistryID}\"
}"

# Use jq to set the dynamic `iat` and `exp`
# fields on the payload using the current time.
# `iat` is set to now, and `exp` is now + 1 second.
PAYLOAD=$(
    echo "${payload}" | jq --arg time_str "$(date +%s)" \
    '
    ($time_str | tonumber) as $time_num
    | .iat=$time_num
    | .exp=($time_num + 60 * 60)
    '
)

function b64enc() { openssl enc -base64 -A | tr '+/' '-_' | tr -d '='; }

function rs_sign() { openssl dgst -binary -sha256 -sign ${KEYFILE} ; }

JWT_HDR_B64="$(echo -n "$HEADER" | b64enc)"
JWT_PAY_B64="$(echo -n "$PAYLOAD" | b64enc)"
UNSIGNED_JWT="$JWT_HDR_B64.$JWT_PAY_B64"
SIGNATURE=$(echo -n "$UNSIGNED_JWT" | rs_sign | b64enc)

echo "$UNSIGNED_JWT.$SIGNATURE" > ${OUTPUTFILE}
```
