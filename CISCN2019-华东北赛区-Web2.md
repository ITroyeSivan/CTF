---
title: '[CISCN2019 华东北赛区]Web2'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201022154259.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-10-22 13:36:28
authorLink:
tags: xss
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-536215.jpg
---
前言
到现在似乎就从来没有见过xss的题目，以至于看到这题的时候才想起来自己对xss只是略知一二。
关于原理这里有一篇付费文章讲的非常不错：https://blog.csdn.net/qq_36119192/article/details/82469035

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201022160718.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201022161932.png)

主页没啥发现，但是通过dirsearch扫到了admin.php，访问提示没有权限。

简单投稿发现我们写的内容会被显示在一个html文件里，同时反馈界面提示admin会访问我们提交的界面，再结合访问admin.php需要权限，可推出这就是xss无疑。我们需要生成一个钓鱼链接使管理员点击，获取到他的session。从而获得进入admin.php的权限。

由于buu无法访问外网，所以需要用到 http://xss.buuoj.cn

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201022162342.png)

将(new Image()).src改为window.location.href，
并且去掉下面if的内容（不是很清楚这一步）

由于过滤了括号，需要编写脚本转换成html markup，即&#加上ascii码。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201022163207.png)

将得到的payload提交，接下来就是反馈给管理员。
注意要将buu网址前面那串代码改成web，这一步我也不知道是为啥。。。
有个md5的简单认证比较简单就不说了。

然后再xss平台收到反馈

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201022163420.png)

成功登录进admin.php
最后是一个弱智sql注入。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201022163513.png)

