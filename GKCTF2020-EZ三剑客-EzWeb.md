---
title: '[GKCTF2020]EZ三剑客-EzWeb'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-08-13 21:10:13
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1095248.jpg
---
根据源码中的提示访问?secret看到了一堆ifconfig的结果：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720200813220350.jpg)

猜测是ssrf(Server-Side Request Forgery:服务器端请求伪造)，之前遇到过一次之后就再也没碰到过。
试一下file协议读文件，但是被过滤了。这里用file:空格//绕过即可。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200813220913.png)

过滤了dict协议：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200813221035.png)

但是还可以用http协议进行内网主机存活探测。
跑出来后提示端口不对，这里我接下来只跑了几百。。。一是因为bp是demo比较垃圾只能一线程跑太多不知道要到啥时候，二是因为对端口这方面了解不是很深没想到会到后面几千。
看wp得知跑出结果应为6379。
百度可知6379是redis的端口
详情：https://www.redteaming.top/2019/07/15/%E6%B5%85%E6%9E%90Redis%E4%B8%ADSSRF%E7%9A%84%E5%88%A9%E7%94%A8/
运行脚本得到ssrf的payload

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200813225748.png)

由于编码问题，最好在框内提交。