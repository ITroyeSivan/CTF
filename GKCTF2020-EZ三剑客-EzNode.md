---
title: '[GKCTF2020]EZ三剑客-EzNode'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-08-22 22:03:06
authorLink:
tags: settime溢出+沙盒逃逸
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1097333.jpg
---
最近两个比赛的题都不会做。。。
太烦了，就是没思路。。。
不多说了，刷题。
_______________________________

对node.js还不是很熟,所以记录一下。
关键代码

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/124314145.jpg)

这一大段主要是在/eval这个路由下，首先他先设置delay的默认值，我们可以去到这个路由把delay的值传过去，
然后他会比较传过去的值和默认值，选较大的一方作为自己的值。然后就是设置超时，将秒数delay作为超时时限，超时了就进到下一个路由

可是我们无论怎么发都不可能超过6秒
问题出在了SetTimeout这个函数
存在溢出

浏览器内部使用32位带符号的整数来储存推迟执行的时间这意味着setTimeout最多延迟2147483647秒。只要大于2147483647,就会发生溢出,就可以绕过那个时间限制，进入下一个路由

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200822232137.png)

他这里要我们post传参e,，就是要让我们沙盒逃逸
开头：const saferEval = require('safer-eval'); // 2019.7/WORKER1 找到一个很棒的库

搜一波这个库的漏洞，搜到一个CVE，直接套用代码即可cat /flag。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200822232310.png)

