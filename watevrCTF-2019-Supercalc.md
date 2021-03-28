---
title: '[watevrCTF-2019]Supercalc'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2020-12-09 19:40:11
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-956929.png
---

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201209194142.png)

计算器类型的题目，一般都是ssti。
题目给出了github的源码，不知道比赛的时候是不是这样的，给出来之后整道题变得非常简单。如下图所示，直接给出了secretkey和命令执行点。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201209194434.png)

首先随便刷新一下看一下给的session：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201209194542.png)

结合源码可知会执行code里面的内容，然后我们又掌握了secretkey，那么就可以伪造session执行命令了。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201209200044.png)

