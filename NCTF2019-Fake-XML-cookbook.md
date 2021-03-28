---
title: '[NCTF2019]Fake XML cookbook'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-07-25 23:05:21
authorLink:
tags: xxe
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1091702.jpg
---
题目提示XML，可知为XXE攻击。

先来学习下xml实体注入攻击：

https://www.freebuf.com/vuls/175451.html

可以看到抓包后下面就是xml格式，接下来增加我们自己的恶意实体，读取/etc/passwd文件

读取flag：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200726220724.png)