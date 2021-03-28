---
title: '[RoarCTF 2019]Simple Upload'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-09-14 20:36:54
authorLink:
tags: thinkphp upload
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1101259.jpg
---
之前写的关机的时候忘保存了全没了。。。

源码

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/654.jpg)

具体过程大致如下，其中知识点第二点有误，评论已经指出。
https://www.cnblogs.com/wangtanzhi/p/12257246.html

————————————————————————

做了三天。。。。
原因是脚本跑不出，懒得去研究多线程，单线程跑。要么实验室网络波动断了，那还好，可以接上次断的地方接着跑。还有就是buu容器时间到了，一下子全没了。。。还有就是上课来不及跑完。。。
今天放弃脚本了，比赛的时候除非多线程跑否则这也太花时间了，看了另一个巧妙的解法。

tp3中在文件上传时会调用一个
strip_tags函数，该函数会去掉文件中的html标签，也即是我们可以使用html标签来绕过check。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200917000013.png)

一秒出。。。直接心态爆炸QWQ
不过这种方法能想到的话多线程应该也不是问题。
菜是原罪。

单线程脚本就不贴了，全网的脚本全跑过了，没一个大佬贴的多线程的，太坏了。

