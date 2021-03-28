---
title: '[HarekazeCTF2019]Easy Notes'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-11-07 21:15:21
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1111656.jpg
---

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201107213120.png)

这题一开始百思不得其解，我就感觉这题没有源码做不了，但是也扫不出泄露的源码。
结果发现buu直接给出了源码。。。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201107213305.png)

首先查看flag.php

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201107213353.png)

is_admin()为true时，输出flag。

在lib.php里面找到了is_admin()的定义：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201107221252.png)

由下图可知文件名是可控的：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201107233140.png)

只要让用户名为sess_，type为.. ，就能创建一个session文件。
那么接下来就是看如何控制文件内容。

在伪造了这个session文件之后，就会向这个文件写入note的title，所以让title等于|N;admin|b:1;即可。|N;用来闭合杂乱数据。xxxx|N;admin|b:1;xxxxxx。

解题：

首先以sess_为登录名登录，接着上传title为|N;admin|b:1;，body随意的笔记。
然后访问export.php?type=. 得到令session admin为true的paylaod。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201108000738.png)

