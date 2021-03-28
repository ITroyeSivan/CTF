---
title: Bugku-never give up
author: Troy3e
avatar: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg
authorLink: 
authorAbout: steamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2020-07-14 00:03:27
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1087062.jpg
---
一开始真没找到突破口，最开始试了sql，然后抓包也没发现啥，除了注释里的1p.html，访问后直接跳转到https://www.bugku.com/，看了wp才知道直接看源码：
view-source:http://123.206.87.240:8006/test/1p.html
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200714001024.png)
经过几次解码得到：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200714001228.png)
关键部分审计：
$data = @file_get_contents($a,'r');
if($data=="bugku is a nice plateform!" and $id==0 and strlen($b)>5 and eregi("111".substr($b,0,1),"1114") and substr($b,0,1)!=4)
id=0，a=bugku is a nice plateform!b的长度要大于5并且第一位是4但第一位又不能是4
啊这？感觉你在玩我。
直接访问f4l2a3g.txt，找到flag
啊这...
