---
title: '[FBCTF2019]RCEService'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-08-04 23:00:09
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1092806.jpg
---

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200804230100.png)

好像是当时比赛给了源码，但buu没有给：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200804230206.png)

过滤极为恐怖，长到直接不想看了。

新知识：
preg_match()函数，只匹配一行，用个换行符搞定：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200804231542.png)

flag：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200804231827.png)