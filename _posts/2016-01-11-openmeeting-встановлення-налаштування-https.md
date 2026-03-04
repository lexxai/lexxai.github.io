---
layout: post
title: "Openmeeting, встановлення, налаштування HTTPS"
date: 2016-01-11 18:57:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2016/01/openmeeting-https.html
---

[![](/assets/images/blog/e548f6f27aa7fcde-c848c92ce32567bf.png)](http://openmeetings.apache.org/images/logos/logo_om1.png)

[Openmeetings](http://openmeetings.apache.org/) забезпечує
проведення відеоконференції, обмін миттєвими повідомленнями, спільна
дошка для малювання, показ робочого столу,спільне редагування документів
та інших засобів групового використання API функцій Red5 Streaming
Server для віддаленої взаємодії і потокового відео.  
[Попередня публікація](http://www.lexxai.pp.ua/2013/03/openmeeting-ssl.html) була про налаштування SSL при використанні протоколу RTMPS.  
Оновив версію OM до 3.0.7 і вирішив налаштувати у додаток до RTMPS і HTTPS.  
Згідно з офіційною [інструкцією.](http://openmeetings.apache.org/RTMPSAndHTTPS.html) Але з'ясував що дещо не працює.  
Так наприклад сертифікати потрібні у теці *tomcat.8080/conf*. Тому і створив посилання на сертифікати котрі існують.  
  
linkKeystore.sh:  

```
mkdir ../tomcat.8080/conf
ln  keystore  /usr/lib/red5/tomcat.8080/conf/keystore
ln  truststore /usr/lib/red5/tomcat.8080/conf/truststore
ln -s  keystore keystore.screen
```

  
P.S. З'ясував що у версії OM 3.0.7, не підійшли нові налаштування RTMPS у файлі: red5/conf/red5-core.xml:   

```
    <bean id="rtmpsMinaIoHandler" class="org.red5.server.net.rtmps.RTMPSMinaIoHandler">
        <property name="handler" ref="rtmpHandler" />
        <property name="codecFactory" ref="rtmpCodecFactory" />
        <property name="keystorePassword" value="${rtmps.keystorepass}" />
        <property name="keystoreFile" value="${rtmps.keystorefile}" />
        <property name="truststorePassword" value="${rtmps.truststorepass}" />
        <property name="truststoreFile" value="${rtmps.truststorefile}" />
        <property name="useClientMode" value="false" />
        <property name="needClientAuth" value="false" />
        <property name="cipherSuites">
            <array>
                <value>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256</value>
                <value>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384</value>

                <value>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA</value>
                <value>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384</value>
                <value>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA</value>
                <value>TLS_ECDHE_RSA_WITH_RC4_128_SHA</value>
                <value>TLS_RSA_WITH_AES_128_CBC_SHA256</value>
                <value>TLS_RSA_WITH_AES_128_CBC_SHA</value>
                <value>TLS_RSA_WITH_AES_256_CBC_SHA256</value>
                <value>TLS_RSA_WITH_AES_256_CBC_SHA</value>
                <value>SSL_RSA_WITH_RC4_128_SHA</value>
            </array>
        </property>
        <property name="protocols">
            <array>
                <value>TLSv1</value>
                <value>TLSv1.1</value>
                <value>TLSv1.2</value>
            </array>
        </property>
    </bean>
```

А ось з старої конфігурації запрацювали.  

```
    <bean id="rtmpsTransport" class="org.red5.server.net.rtmp.RTMPMinaTransport" init-method="start" destroy-method="stop">
        <property name="ioHandler" ref="rtmpsMinaIoHandler" />
                <property name="addresses">
                    <list>
                         <value>${rtmps.host}:${rtmps.port}</value>
                    </list>
                </property>
        <property name="ioThreads" value="${rtmp.io_threads}" />
        <property name="tcpNoDelay" value="${rtmp.tcp_nodelay}" />
    </bean>
```

Чому так я не знаю, не фахівець, тому залишив як є.  Може з часом вирішиться.
