---
title: '[CISCN2019 华东南赛区]Web11'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-08-05 22:21:24
authorLink:
tags: ssti smarty模板
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1092341.jpg
---
明天科目二。今天练了一天早点休息了。。。
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200805222449.png)
看这题目名字和这界面一开始以为很难是新知识点，直接百度了。。。
仔细看了才知道原来就是ssti。（主要是smarty模板之前没见过没反应过来）
知道了ssti就好办了，右上角显示了我们的ip，下面又有XFF。
猜测是在XFF处发生ssti。
smarty常用payload：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200805223702.png)
