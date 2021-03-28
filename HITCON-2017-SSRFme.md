---
title: '[HITCON 2017]SSRFme'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-08-12 00:24:13
authorLink:
tags: GET命令执行漏洞
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1094203.jpg
---

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200812005103.png)

explode(separator,string)函数把以separator为分隔字符串将字符串打散为数组。

“REMOTE_ADDR”为正在浏览当前页面用户的 IP 地址。

escapeshellarg()把字符串转码为可以在 shell 命令里使用的参数

pathinfo() 函数以数组的形式返回文件路径的信息。包括以下的数组元素：
 [dirname] //路径名
 [basename] //文件名
 [extension] //扩展名

basename() 函数返回路径中的文件名部分。

根据源码可以发现php会对传过去的参数用escapeshellarg函数过滤。先创建一个目录sandbox/md5(orange+ip)，然后执行GIT $_GET['url']，然后会创建文件夹，并将执行GIT $_GET['url']后的结果放在该文件夹下面filename传过去的文件中。

一、GET ./可以查看当前路径，GET …/可以查看上一级路径
?url=../../../../../../&filename=a或者?url=/&filename=aaa
然后访问可以看到flag和readflag
猜想执行readflag flag可以得到flag，接下来就是如何构造并执行了。

二、利用perl的open命令有可能会导致命令执行
perl在open当中可以执行命令，如:
 open(FD, "ls|")或open(FD, "|ls")
都可以执行ls命令，而GET是在perl下执行的，当GET使用file协议的时候就会调用到perl的open函数，这就是我们要利用的点，
利用bash -c "cmd string"来执行命令执行readflag。（不用bash -c可以直接/readflag读取flag）
1、首先得满足前面的文件存在, 才会继续到open语句, 所以在执行命令前得保证有相应的同名文件:
2、?url=&filename=bash -c /readflag| 先新建一个名为“bash -c /readflag|”的文件，用于之后的命令执行
3、?url=file:bash -c /readflag|&filename=aaa 再利用GET执行bash -c /readflag保存到111文件
4、访问sandbox/md5/aaa（得到flag）
