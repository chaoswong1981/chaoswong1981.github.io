---
title: "用Qt实现https访问"
date: 2024-06-27T14:32:19+08:00
draft: false
tags: ['Qt']
---

项目上需要访问后台网站的一个网址，它会返回一个二维码，然后把它显示出来。项目是基于Qt Mingw开发的，网站是基于https的。

一开始用Qt实现了一下访问http的图片，没啥问题：
```c++
//同步的方式
void MainWindow::showQRCode()
{
    QString url = "http:/xxxx/test.png";

    //初始化
    QNetworkAccessManager networkMgr;
    QNetworkRequest req;
    req.setUrl(url);

    //阻塞等待
    QEventLoop eventLoop;
    connect(reply, &QNetworkReply::finished, &eventLoop, &QEventLoop::quit);
    eventLoop.exec(QEventLoop::ExcludeUserInputEvents);

    //发起GET请求，获取图片
    QNetworkReply* reply = networkMgr.get(req);
    QByteArray bytes = reply->readAll();
    QPixmap pixmap;
    pixmap.loadFromData(bytes);

    //显示到界面上
    ui->label_wx_qrcode->setPixmap(pixmap);
}
```

但在访问https时，报错了：
```shell
qt.network.ssl: QSslSocket::connectToHostEncrypted: TLS initialization failed
qt.network.ssl: QSslSocket::connectToHostEncrypted: TLS initialization failed
```

打印了一下replay->error()：
```shell
QNetworkReply::UnknownNetworkError
```

猜测可能是Qt不支持https的原因，网上找了段代码测试：
```c++
qDebug() << "Support SSL:  " << QSslSocket::supportsSsl()
            << "\nLib Version Number: " << sslSocket::sslLibraryVersionNumber()
            << "\nLib Version String: " << QSslSocket::sslLibraryVersionString()
            << "\nLib Build Version Number: " << QSslSocket::sslLibraryBuildVersionNumber()
            << "\nLib Build Version String: " << QSslSocket::sslLibraryBuildVersionString();
```

输出结果：
```shell
Support SSL:   false 
Lib Version Number:  0 
Lib Version String:  "" 
Lib Build Version Number:  269488175 
Lib Build Version String:  "OpenSSL 1.1.1b  26 Feb 2019"
```
可见目前的Qt版本（5.12.6）是支持OpenSSL的，但是却查不到库的版本信息，应该是缺少对应的库。

针对这个问题，[网上给的建议](https://stackoverflow.com/a/58382414/1589056)：  
1. 在 https://www.openssl.org/source/ 上下源码，手动编
2. 去 https://wiki.openssl.org/index.php/Binaries 下找对应的库下载

windows下最好还是不要去编译三方库，太折腾了，所以先去找预编译好的库。

在 https://wiki.openssl.org/index.php/Binaries 上面可以看到三个网站提供库：
- https://slproweb.com/products/Win32OpenSSL.html
- http://wiki.overbyte.eu/wiki/index.php/ICS_Download
- https://www.firedaemon.com/get-openssl

**注意：**  
这里有个坑，你要**看清楚上面输出的结果里，当前Qt版本链接的是哪个版本的库**，比如这里我用的Qt5.12.6链接的是`OpenSSL 1.1.1b`，所以只能去找这个版本才能用。而上面第一个网址里提供的是3.x版本的库，装这个是没用的。

所以在第二个网址里找到[OpenSSL 1.1.1w](https://wiki.overbyte.eu/arch/openssl-1.1.1w-win64.zip)版本（小版本无所谓），下载下来。将其中的`libcrypto-1_1-x64.dll`和`libssl-1_1-x64.dll`拷贝到Qt的安装目录，比如我的是：`C:\Qt\Qt5.12.6\5.12.6\mingw73_64\bin\`下即可。

这个时候再去运行测试代码，应该能发现支持ssl了：
```shell
Support SSL:   true 
Lib Version Number:  269488511 
Lib Version String:  "OpenSSL 1.1.1w  11 Sep 2023" 
Lib Build Version Number:  269488175 
Lib Build Version String:  "OpenSSL 1.1.1b  26 Feb 2019"
```

再尝试访问https，应该就可以了。