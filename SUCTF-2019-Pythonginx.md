---
title: '[SUCTF 2019]Pythonginx'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-07-17 14:42:03
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1088601.png
---

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200717144532.png)

应该要绕过两个if才会输出flag，简单来说就在前两个判断时不能是suctf.cc，第三个是suctf.cc。
看了wp，这里用的是脚本爆破。
脚本见 https://www.cnblogs.com/Cl0ud/p/12187204.html
当URL 中出现一些特殊字符的时候，输出的结果可能不在预期。
随便用一个来替换最后一个c就可以绕过了。
/getUrl?url=file://suctf.c%E2%84%82/../../../../../etc/passwd

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200717153917.png)

看到了nginx，于是去读取nginx的配置文件
 /usr/local/nginx/conf/nginx.conf
看到了flag