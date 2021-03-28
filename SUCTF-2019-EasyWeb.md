---
title: '[SUCTF 2019]EasyWeb'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-08-07 23:28:32
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1094205.jpg
---
这题实在太难了，本来想记录一下的，无奈实在是讲不清楚。
知识点：
1、构造不包含数字和字母的webshell
2、文件上传绕过
3、绕过open_basedir/disable_function

勉强看懂。

——————————————————

突然发现了个大佬给了个特别简单的方法？突然感觉能做了。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/1431441.jpg)

异或绕过下面的那部分，然后直接ctrl+f在phpinfo找到flag。。。
算是非预期了吧。

这两天在搭招新平台，基本没什么问题了，就是还不会出题。。。


