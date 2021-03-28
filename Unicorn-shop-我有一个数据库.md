---
title: Unicorn shop&我有一个数据库
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-07-18 22:26:37
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1088927.jpg
---
两道比较简单的题目
一、[ASIS 2019]Unicorn shop

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200718215406.png)

一个购买独角兽的商店，购买前三个都会提示商品错误，，估计是要购买第四个，但是买第四个会提示只能输入一位，应该是要找一个一位的大于1337的字符。
考点unicode，搜unicode大于1337的字符。
id=4&price=%E2%86%82

flag{4fc1bf7c-2b3c-4b3f-a418-bcab67ea145c} 


二、[GWCTF 2019]我有一个数据库

dir扫到了phpmyadmin，直接访问，成功。
但是进去以后不知道咋办了。。。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200718221913.png)

看了wp知道要查看版本信息去找对应的漏洞。
没想到这么简单粗暴。

https://www.jianshu.com/p/fb9c2ae16d09

可以发现在index.php里：
include $_REQUEST['target'];
include这个函数,很可能存在本地文件包含
链接里已经给出了相关poc
我们直接利用即可
paylaod:http://c66c8c33-f4ec-4bb4-b351-3302f06c8bdc.node3.buuoj.cn/phpmyadmin/?target=db_datadict.php%253f/../../../../../../../../flag


