---
title: '[HFCTF2020]BabyUpload'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-10-30 13:40:11
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-608183.jpg
---
源码

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201030194832.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201030194845.png)

首先分析输出flag的条件，只要filename符合条件且存在，那么就会输出flag。

两个post参数。attr拼接成为dirpath，direction的值分为upload和download。
当direction为upload时，首先判断文件是否能正常上传，然后拼接文件名，接着对文件名进行sha256加密。还限制了目录穿越，创建新目录并将文件上传至该目录下。
当direction为download时，和上面差的不多。

总结一下可知我们获取flag需要两个条件。一是session username为admin。二是创建一个success.txt文件。

首先burp抓包查看一下我的phpsessid

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201030225232.png)

首先尝试传入：direction=download&attr=&filename=sess_ab3979c79b41963162a6a63cbf0931c4

回显我的username是guest。至于如何伪造成admin就无从而知了。
查看wp得知他们看到burp上有个不可见字符从而想打破不同引擎对应着不同session的存储方式。
但是我的burp没有乱码。。。

php_binary:存储方式是，键名的长度对应的ASCII字符+键名+经过serialize()函数序列化处理的值
php:存储方式是，键名+竖线+经过serialize()函数序列处理的值
php_serialize(php>5.5.4):存储方式是，经过serialize()函数序列化处理的值

有不可见字符的话 那就能说明我那里为啥是空白的了 很显然是php_binary ascii码中有不可见字符。
那么我们可以在本地利用php_binary生成我们要伪造的session文件。

//<?php
ini_set('session.serialize_handler', 'php_binary');
session_save_path("E:\\phpstudy_pro\\WWW\\");
session_start();

$_SESSION['username'] = 'admin';

生成之后查看sha256加密后的文件名

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201030233207.png)

432b8b09e30c4a75986b719d1312b63a69f1b833ab602c9ad5f0299d1d76a5a4

接下来就是上传文件

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201030234150.png)

然后验证一下是否上传成功

direction=download&attr=&filename=sess_432b8b09e30c4a75986b719d1312b63a69f1b833ab602c9ad5f0299d1d76a5a4

得到正确回显： usernames:5:"admin";

这样就实现了伪造

接下来创建success.txt然后出发读flag机制即可。
flag{8fa47d71-db92-449e-b24f-1bb93820bbd5}