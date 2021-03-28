---
title: '[网鼎杯 2020 青龙组]AreUSerialz'
author: Troy3e
avatar: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg
authorLink: 
authorAbout: 
authorDesc: 
categories: 技术
comments: true
date: 2020-07-08 15:23:51
tags: php反序列化
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-556382.jpg
---
前一阵子学了反序列化，好久没有练习了，正好拿这道题练练手。
先上源码：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200708204723.png)
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200708204952.png)
get方式提交的str，会对其进行一个合法性的判断，每一个字符的ASCII范围在32到125之间，然后对其反序列化。
反序列化时首先会调用__destruct()函数，__destruct()会检测op值是否为'2'，如果为'2'就会令op=1，由于是===必须是类型和数值都等于'2',所以可以让op等于数字2来绕过，然后__destruct()会调用process()，process()中如果op值为2将会执行read()函数，会读取fliename的文件，所以我们需要将op=2，filename='php://filter/read=convert.base64-encode/resource=flag.php'（文件包含漏洞）进行序列化，序列化结果为str=O:11:"FileHandler":3:{s:2:"op";i:2;s:8:"filename";s:57:"php://filter/read=convert.base64-encode/resource=flag.php";s:7:"content";N;}
最后base64解码即可

