---
title: buu知识点复习(二)
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-08-30 10:03:39
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1098426.jpg
---
一、[SUCTF 2019]CheckIn--upload
当时第一次见到修改.user.ini的题，但是看了好多wp都没有讲怎么会想到用这个的。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/sdhehe.jpg)

通过给上传脚本加上相应的幻数头字节就可以绕过exif_imagetype()：
JPG ：FF D8 FF E0 00 10 4A 46 49 46
GIF(相当于文本的GIF89a)：47 49 46 38 39 61
PNG： 89 50 4E 47

<?被过滤这样绕过：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200830104622.png)

二、[CISCN2019 华北赛区 Day2 Web1]Hack World--python脚本
python是要重点学习的地方，因为第一遍做的时候只是看懂了而已，这次要做到能自己写出来。
以这道题为例，在已经告诉我们表和列的情况下，其实是一个非常简单的二分法脚本。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200830133702.png)

一些注意点：
1、import time应该是想加延时函数来针对buu的waf的，但这里没有用到。
2、记住一些书写格式。
比如payload={"xx":""}
html=requests.post(url,data=payload)
3、判断。
0^0为0，0^1为1。1的时候会出现"Hello"。

三、[ZJCTF 2019]NiZhuanSiWei--php
记录一下不常用的php伪协议
if(isset($text)&&(file_get_contents($text,'r')==="welcome to the zjctf"))
这里需要我们传入一个文件且其内容为welcome to the zjctf，这样的话往后面看没有其他可以利用的点，我们就无法写入文件再读取，就剩下了一个data伪协议。data协议通常是用来执行PHP代码，然而我们也可以将内容写入data协议中然后让file_get_contents函数取读取。构造如下：
text=data://text/plain;base64,d2VsY29tZSB0byB0aGUgempjdGY=

四、[BJDCTF2020]Easy MD5
select * from 'admin' where password=md5($pass,true)

强网杯也遇到了这个知识点，可惜前面md4没撞出来，太菜了。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200830141357.png)

为什么ffifdyop就是答案，因为ffifdyop的md5的原始二进制字符串里面有‘or’6这一部分的字符。
还有其他的答案，就不一一列举了。







