---
title: '[WesternCTF2018]shrine'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-07-21 23:06:20
authorLink:
tags: ssti
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1086450.png
---
整理源码得：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/afafsafasff.png)

看到了jinja，下意识想到了ssti。
在shirine路径下测试ssti：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200721231031.png)

分析源码，注册了一个名为FLAG的config，猜测这就是flag，如果没有过滤可以直接{{config}}即可查看所有app.config内容，但是这题设了黑名单[‘config’,‘self’]并且过滤了括号。
当config,self,()都被过滤的时候，为了获取讯息，我们需要读取一些例如current_app这样的全局变量。
这里有两个函数包含了current_app全局变量，url_for和get_flashed_messages。
注入{url_for.__globals__}看到current_app
 current_app是当前使用的app，继续注入当前app的config
{url_for.__globals__['current_app'].config}

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/2131123.png)
