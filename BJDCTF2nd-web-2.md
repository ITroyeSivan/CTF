---
title: BJDCTF2nd-web-2
author: Troye
avatar: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg
authorLink: 
authorAbout: 
authorDesc: 
categories: 技术
comments: true
date: 2020-04-13 22:17:18
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1072905.jpg
---
4、假猪套天下第一
是一个登陆界面，首先burp抓包发现L0g1n.php，访问显示如下：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/rhth.jpg)
又刷新了一遍变成了：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/2.jpg)
猜测是要修改时间才能访问。我直接在cookie里的time值后面加了一个0。后面就是加各种header头，需要注意的就是伪造的ip头是client-ip,需要多试几遍。
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/3.jpg)
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/4.jpg)
base64。
5、简单注入
没思路。。。
看了wp发现居然线索是藏在robots.txt里的，好久没遇到藏在robots.txt里的题所以居然忘记尝试了。根据提示访问hint.txt：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/3344.jpg)
可能存在sql注入
首先fuzz一波看下过滤了什么（）
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/77.jpg)
看不懂，根据大佬wp提示，没有过滤\，由于引号被过滤，所以我们可以使得username=admin&password=or 1#
发现回显发生变化
接下来就是上脚本了，这里因为看不懂我就不放了，还需要学习。
