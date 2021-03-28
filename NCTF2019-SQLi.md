---
title: '[NCTF2019]SQLi'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-09-12 14:07:25
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/bdnznnz.jpg
---
hint.txt

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200912143016.png)

获得admin的密码即可获取flag

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200912150536.png)

regexp

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200912152906.png)

大佬的脚本：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200912153819.png)

string.ascii_lowercase是小写的26个字母
string.ascii_uppercase是大写的26个字母
string.digits是十进制数字常数

其实是一道很简单的sql。。。
被之前那道题吓怕了以为这个会很难就直接看了wp
太后悔了。。。

---------------------------
这个脚本感觉有问题，密码是错的，应该是全小写，不知道为什么能跑出来大写？
玄学问题。