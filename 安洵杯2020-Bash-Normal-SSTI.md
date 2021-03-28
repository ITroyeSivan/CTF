---
title: 安洵杯2020-Bash&Normal SSTI
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-11-28 23:28:35
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1115554.jpg
---
安洵杯2020-Bash&Normal SSTI

一、Bash
早上实验课瞅了一眼，第一感觉是挺简单的，打算下午下课了把它c了。但是下午一看题解发现不对劲，怎么只有3解？大佬们都做不出来的东西肯定不简单，于是就放弃了。
看了wp受益匪浅，确实是很少见的绕过，只能说师傅tql。
linux的位运算以及进制的转换之前从来没见过：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201128142200.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201128142300.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201128142340.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201128142418.png)

二、Normal SSTI

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201128232932.png)

这个ssti还没仔细研究，有空看下。也是从没出现过的一种方法。