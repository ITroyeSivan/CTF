---
title: '[安洵杯 2019]easy_web'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-07-18 15:31:26
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1089035.png
---

http://abe9b6ec-e237-45f1-9ec3-36ab54c41dbb.node3.buuoj.cn/index.php?img=TXpVek5UTTFNbVUzTURabE5qYz0&cmd=

img貌似是base64处理过的字符串，解码得到MzUzNTM1MmU3MDZlNjc=，继续解码得到3535352e706e67，是十六进制的ascii码，解码得555.png。按照这思路加密index.php尝试读取。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200718155752.png)

前面的都不重要，重要的是cmd的部分。
a和b md5强类型绕过，网上搜一个就行。
cmd过滤了很多东西，但是还是可以用dir，在根目录找到了flag（空格被过滤，用%20代替空格）

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200718162139.png)

cmd=/bin/c\at%20/flag

flag{a8e20fc1-9183-4ab7-8d20-2bf697f447b4}