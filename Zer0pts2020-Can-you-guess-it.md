---
title: '[Zer0pts2020]Can you guess it?'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-08-18 21:05:56
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1096637.jpg
---

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200818210501.png)

可以很明显的看到flag的获取方式，可是仔细看下就知道不太可能实现。。。64位的随机数，应该只是用来吓唬人的。

那咋办呢，当然是查看wp了。
回到开头那一段

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200818212055.png)

我们要造成任意文件读取，需要绕过正则匹配和basename

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/rtsjrtysjkmsrt.jpg)

$_SERVER[‘PHP_SELF’]表示当前执行脚本的文件名，当使用了PATH_INFO时，这个值是可控的。所以可以尝试用/index.php/config.php?source来读取flag。

但是正则过滤了/config.php/*$/i。
从 https://bugs.php.net/bug.php?id=62119 找到了basename()函数的一个问题，它会去掉文件名开头的非ASCII值：
var_dump(basename("xffconfig.php")); // => config.php
var_dump(basename("config.php/xff")); // => config.php
所以这样就能绕过正则了，payload：
/index.php/config.php/%ff?source
（不可见字符来进行绕过，而超出ascii识别的访问，basename能够正常访问config.php）

