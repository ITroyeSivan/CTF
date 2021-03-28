---
title: '[GYCTF2020]FlaskApp'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-08-03 20:45:17
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-704039.png
---
一个奇慢无比的网站。。。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200803205513.png)

base64decode在不会解析的时候就会报错。由报错可以读到部分代码。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200803205755.png)

关键代码如上图。get传参text，如果绕过waf则可以执行。
尝试ssti注入
在加密界面加密{{6+6}}得e3s2KzZ9fQ==
解密得12
于是构造语句，

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200804000050.png)

进行文件读取


ssti命令总结：https://blog.csdn.net/weixin_43536759/article/details/105066445

读源码:

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200803235941.png)


![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200803214509.png)

import和os被过滤，但是可以用字符串拼接进行绕过：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200804000000.png)

发现了this_is_the_flag.txt

读取使用切片省去了拼接flag的步骤：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/fgbdsgbsehb.jpg)


总结：ssti注入知识积累不够，待会儿就去总结一下这方面的知识。


