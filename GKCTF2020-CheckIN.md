---
title: '[GKCTF2020]CheckIN'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-07-17 21:49:07
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1087056.jpg
---

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200717215909.png)

看到了eval，想到了一句话，给Ginko传参，但是要先经过base64加密。
有一个一开始不太清楚的地方，导致卡住了：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200717222600.png)

首先传参phpinfo();看看，需要经过base64编码

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200717222903.png)

试着上传一个一句话木马

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200717231759.png)

找到了flag！但是打开啥都没有。。。
下面readflag打开是乱码。

查看wp

通过phpinfo()知道php版本为7.3，这个版本有一个漏洞
php7-gc-bypass漏洞利用PHP garbage collector程序中的堆溢出触发进而执行命令
影响范围是linux，php7.0-7.3
给出了exp
https://github.com/mm0r1/exploits/blob/master/php7-gc-bypass/exploit.php
下载后进行修改，改为执行readflag
通过蚁剑上传至tmp目录下（因为这目录的权限较高），上传成功后在页面里包含文件即可获得flag

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200717232904.png)



参考链接：https://blog.csdn.net/m0_46230316/article/details/106477417