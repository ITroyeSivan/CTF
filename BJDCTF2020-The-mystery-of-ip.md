---
title: '[BJDCTF2020]The mystery of ip'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-07-19 23:21:06
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/fwefwegfw.jpg
---
在hint.php找到了如下注释：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200719230950.png)

flag.php显示你的ip
尝试抓包修改X-Forwarded-For，果然随着我们的修改而改变，试一下是不是ssti：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200719231331.png)

存在ssti。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/dawefwfqwef.png)

现在发现有时候不是题目不会做，而是没想到是这个方法，看来还是太菜了。