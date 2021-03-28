---
title: '[GYCTF2020]EasyThinking'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-09-12 20:32:23
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1100620.jpg
---
www.zip源码泄露
看到是thinkphpV6框架，立刻网上去找现成的漏洞。

第一次找到了--ThinkPHP v6.0.x 反序列化漏洞利用

https://www.cnblogs.com/-chenxs/p/12020777.html

但试了一下这题好像不行，没有参数无法上传payload。

然后又看到了--ThinkPHP6.0 任意文件操作

http://www.secflag.com/archives/606.html


![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200912214311.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200912214329.png)

细节就不多说了，放个链接备忘：
https://blog.csdn.net/mochu7777777/article/details/105160796/

有disable_function限制，php版本7.3.11
disable_function绕过exp:
https://github.com/mm0r1/exploits/tree/master

修改命令为readflag，蚁剑上传，然后包含一下即可出flag。

这种都有现成的洞的题做起来实在是太爽了。