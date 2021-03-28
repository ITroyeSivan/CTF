---
title: '[BJDCTF2020]Mark loves cat'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-07-19 21:38:40
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/geragggewrag.png
---
今天第一次去练车，在驾校呆了一天，今天就少做一点题了。
手动找了一圈没找到线索。（本来以为最下面的提交界面是突破口）
于是dir启动：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200719213805.png)

githack启动：
虚拟机又炸了，估计下次得重装了，这里就直接百度了：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200719222215.png)

首先post：$flag=flag
这样根据源码，$$flag=flag
然后get：yds=flag
这样就变成了：$yds=$flag
根据
if(!isset($_GET['flag']) && !isset($_POST['flag']))
{
exit($yds);
}
就会执行exit($flag),即可输出flag。

