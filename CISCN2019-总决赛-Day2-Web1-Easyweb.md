---
title: '[CISCN2019 总决赛 Day2 Web1]Easyweb'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-07-30 23:09:56
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-285333.jpg
---
一个登录界面，没找到任何线索。
dir扫到robots.txt，随着提示在image.php.bak处发现源码

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200730233733.png)

和昨天那个二次注入有点像，有了昨天的基础，大致有了思路：

addslashes()函数，这个函数会把特殊的字符转义。
比如:单引号会被转义成\',斜杠会转义为\\\\.
第十行的str_replace会把"\\\\0","%00","\\\\'","'"中的任意一个替换成空。
我们可根据这个绕过当传入id=\\\\0时，就会在 查询语句处改变sql语句。
即:select * from images where id=' \' or path='+{$path}'
所以我们可以在path处注入我们的新语句.

参考的脚本

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200731000308.png)

爆破得到了用户名和密码，登陆进去是一个上传界面：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200731001219.png)

抓包的时候有提示说用户名写进了log.php，既然是写入PHP，我们就想到写入一个PHP木马。
用<?= ?>代替<?php ?>即可。

但这里有个疑惑，为啥直接写在文件名可以而写在文件内不行。。。难道只是这个文件名，会被写入日志文件中去？

flag蚁剑连接后在根目录。