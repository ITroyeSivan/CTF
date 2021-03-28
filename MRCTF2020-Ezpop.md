---
title: '[MRCTF2020]Ezpop'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-08-05 22:48:39
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1091680.jpg
---
反序列化。说起反序列化，突然想起来之前的很多知识点都有点忘了，比如字符逃逸那块，明天回去复习一下。
源码：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/fwagawegg.jpg)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200805230718.png)

Modifer类，有include，可以通过伪协议读取flag.php文件

__invoke方法，调用函数的方式调用一个对象时的回应方法

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200805231007.png)

看到有__toString方法，类被当成字符串时的回应方法

__wakeup方法，unserialize反序列化时优先调用

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200805231117.png)

__get()方法，访问不存在的属性或是受限的属性时调用

pop链构造

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200805231510.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200805234209.png)