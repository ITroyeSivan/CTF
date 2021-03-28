---
title: '[GWCTF 2019]枯燥的抽奖'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-07-27 21:34:02
authorLink:
tags: php mt_srand() 随机种子
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-320623.png
---

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ图片20200727214004.jpg)

查看源码看到check.php

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200727214256.png)

mt_srand()函数

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200727214807.png)

先用脚本将伪随机数转换成php_mt_seed可以识别的数据

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200727224217.png)

爆破出伪随机数和php版本

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200727224257.png)

再还原：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ图片20200727224541.jpg)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200727224850.png)