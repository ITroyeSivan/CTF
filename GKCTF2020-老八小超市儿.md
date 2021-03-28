---
title: '[GKCTF2020]老八小超市儿'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-07-24 13:47:03
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1091060.png
---
网页是个购物网站模板，后台是admin.php
查询得知ShopXO默认密码为shopxo
成功进入后台。
接下来就在应用中心里的应用商店找到主题，然后下载默认主题。
写一个一句话进去，放在_static_里面，根据首页头像连接可以推测出路径，尝试访问成功后蚁剑连上去：
找到了个假flag：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ图片20200724151850.jpg)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200724152131.png)

尝试打开root目录但是权限不够。
这时看到了一个红色的文件。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200724152254.png)

找到并修改执行的文件，等待60秒后即可再flag.hint里看到flag。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200724152949.png)