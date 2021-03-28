---
title: '[网鼎杯2018]Unfinish'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-08-25 21:02:32
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-748350.png
---
进去是个login.php，提示用户名或密码错误。
猜测有register.php，成功访问。
登陆的时候用到的是邮箱和密码，而注册的时候还有一个用户名，而这个用户名却在登陆后显示了，所以我们考虑用户名这里可能存在 二次注入
所以构造 payload 如下：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/hbsbshbn.jpg)

进行两次hex解码后得到数据库名为web

至于为什么用两次hex：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/shsrhhg.jpg)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200825225814.png)

使用的脚本：https://blog.csdn.net/bmth666/article/details/105499305
要我写肯定写不出这么。。复杂？的脚本。。

但是自己的思路一直出问题，不知道是哪里有问题。。感觉理论上行得通啊：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200825230319.png)

跑是跑出来了，但是感觉不完整，也有可能是多了。

——————————————————————
后来研究了一下，我这个（其实也不是自己的）简单思路其实也是对的，只是输出的时候有点乱，当时可能少选了。
