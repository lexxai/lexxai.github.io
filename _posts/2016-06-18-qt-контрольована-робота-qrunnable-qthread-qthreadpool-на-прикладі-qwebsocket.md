---
layout: post
title: "Qt контрольована робота QRunnable, QThread, QThreadPool, на прикладі QWebSocket"
date: 2016-06-18 01:11:00 +0000
tags: ["programming", "Qt", "програмування"]
blogger_orig_link: https://lexxai.blogspot.com/2016/06/qt-qrunnable-qthread-qthreadpool.html
---

За допомогою [статті](http://www.walletfox.com/course/qrunnableexample.php) можна зробити контрольовану роботу QRunnable та QThreadPool у Qt.  
Тобто передати результати роботу зовнішньому СЛОТУ.  
Через симуляцію connect:
  

```
QMetaObject::invokeMethod(receiver, "updateProgressBar",
                                      Qt::QueuedConnection,
                                      Q_ARG(int, pFinished));
```

  
  
[How to report progress and abort a long running operation with QRunnable and QThreadPool](http://www.walletfox.com/course/qrunnableexample.php)  
  
P.S. Не забуваємо про попереднє використання qRegisterMetaType.  
на кшталт    

```
qRegisterMetaType<QWebSocket*>(); 
```

Адаптована програма з прикладів Qt з використанням QRunnable де нове з'єднання буде відпрацьовувати окремий код у окремому потоці, що дозволить приймати інші запити від QWebSocket.  
  
  

### server.h:

```
#ifndef SERVER_H
#define SERVER_H

#include <QtCore/QObject>
#include <QtCore/QList>
#include <QtCore/QByteArray>

#include <QRunnable>
#include <QWebSocket>

QT_FORWARD_DECLARE_CLASS(QWebSocketServer)
QT_FORWARD_DECLARE_CLASS(QWebSocket)

class EchoServer : public QObject
{
    Q_OBJECT
public:
    explicit EchoServer(quint16 port, bool debug = false, QObject *parent = Q_NULLPTR);
    ~EchoServer();

Q_SIGNALS:
    void closed();
    void command(QString command, QString &commandAnswer);

private Q_SLOTS:
    void onNewConnection();
    void processTextMessage(QString message);
    void postProcessTextMessage(QWebSocket *socketDescriptor);
    void postProcessTextMessage();
    void processBinaryMessage(QByteArray message);
    void socketDisconnected();

private:
    QWebSocketServer *m_pWebSocketServer;
    QList<QWebSocket *> m_clients;
    bool m_debug;
};
```

SubSocket:
  

```
class SubSocket : public QRunnable
{
public:
    explicit SubSocket(/*QObject *parent = Q_NULLPTR*/);
public slots:
//    void process();
//    void processTextMessage(QString message);
//    void socketDisconnected();
signals:
    //void finished(QWebSocket *socketDescriptor);

public:
    QWebSocket *socketDescriptor=Q_NULLPTR;
    EchoServer *receiver=Q_NULLPTR;
    virtual void run();


private:
    //QList<QWebSocket *> m_clients;
    bool m_debug=true;
};

#endif //ECHOSERVER_H
```

### server.cpp:

```
#include "server.h"
#include "QtWebSockets/qwebsocketserver.h"
#include "QtWebSockets/qwebsocket.h"
#include <QtCore/QDebug>
#include <QtCore/QThread>
#include <QThreadPool>

QT_USE_NAMESPACE

//! [constructor]
EchoServer::EchoServer(quint16 port, bool debug, QObject *parent) :
    QObject(parent),
    m_pWebSocketServer(new QWebSocketServer(QStringLiteral("Echo Server"),
                                            QWebSocketServer::NonSecureMode, this)),
    m_clients(),
    m_debug(debug)
{

    qRegisterMetaType<QAbstractSocket::SocketState>();
    qRegisterMetaType<QWebSocket*>();
    qDebug()<<"EchoServer()"<<QThread::currentThread();

    if (m_pWebSocketServer->listen(QHostAddress::LocalHost, port)) {
        if (m_debug)
            qDebug() << "Echoserver listening on port" << port;
        connect(m_pWebSocketServer, &QWebSocketServer::newConnection, this, &EchoServer::onNewConnection);
        connect(m_pWebSocketServer, &QWebSocketServer::closed, this, &EchoServer::closed);
    }
}
//! [constructor]

EchoServer::~EchoServer()
{
    m_pWebSocketServer->close();
    qDeleteAll(m_clients.begin(), m_clients.end());
}

//! [onNewConnection]
void EchoServer::onNewConnection()
{
    QWebSocket *pSocket = m_pWebSocketServer->nextPendingConnection();

    connect(pSocket, &QWebSocket::textMessageReceived, this, &EchoServer::processTextMessage);
    connect(pSocket, &QWebSocket::binaryMessageReceived, this, &EchoServer::processBinaryMessage);
    connect(pSocket, &QWebSocket::disconnected, this, &EchoServer::socketDisconnected);

    m_clients << pSocket;
}
//! [onNewConnection]

//! [processTextMessage]
void EchoServer::processTextMessage(QString message)
{
    QWebSocket *pClient = qobject_cast<QWebSocket *>(sender());
    if (m_debug)
        qDebug() << "Message received:" << message;
    if (pClient) {
        int sentbytes=pClient->sendTextMessage("OK");
        pClient->flush();
        SubSocket *ss=new SubSocket();
        ss->socketDescriptor=pClient;
        ss->receiver=this;
        QThreadPool::globalInstance()->start(ss);
        sentbytes=pClient->sendTextMessage("POST SubSocket");
        pClient->flush();
    }
}

void EchoServer::postProcessTextMessage(QWebSocket *socketDescriptor)
{
    qDebug()<<"EchoServer::postProcessTextMessage(QWebSocket *socketDescriptor)"<<QThread::currentThread()<<socketDescriptor;
    if (socketDescriptor != Q_NULLPTR){
        socketDescriptor->sendTextMessage("COMMAND PROCEED");
        socketDescriptor->sendTextMessage("END");
        socketDescriptor->close();
    }
}

//! [processTextMessage]

//! [processBinaryMessage]
void EchoServer::processBinaryMessage(QByteArray message)
{
    QWebSocket *pClient = qobject_cast<QWebSocket *>(sender());
    if (m_debug)
        qDebug() << "Binary Message received:" << message;
    if (pClient) {
        pClient->sendBinaryMessage(message);
    }
}
//! [processBinaryMessage]

//! [socketDisconnected]
void EchoServer::socketDisconnected()
{
    QWebSocket *pClient = qobject_cast<QWebSocket *>(sender());
    if (m_debug)
        qDebug() << "socketDisconnected:" << pClient;
    if (pClient) {
        m_clients.removeAll(pClient);
        pClient->deleteLater();
    }
}
//! [socketDisconnected]
```

```
SubSocket::SubSocket()
{

}

void SubSocket::run()
{
    qDebug()<<"SubSocket::run()"<<QThread::currentThread();
    qDebug()<<"SubSocket::run() sleep start";
    QThread::sleep(10);
    qDebug()<<"SubSocket::run() sleep stop";

    QMetaObject::invokeMethod(receiver, "postProcessTextMessage",
                              Qt::QueuedConnection,
                              Q_ARG(QWebSocket*, socketDescriptor));
}
 
```
