---
title: '[GWCTF 2019]你的名字'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-11-29 21:02:31
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-737151.png
---

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/52626.jpg)

此题对我来说意义重大：

一是因为宫水三叶是我老婆（，二是因为这是dd在buu上的最后一个一分题了！
数了一下，161道题，从上学期做到现在，属实快乐（大一下学期我划水了我是废物QAQ
有一说一，圈子里能刷完buu所有一分题的Web选手，至少也不算个萌新了，但为什么我还是啥都不会呢，呜呜呜。
要是有一天能y1ng那种大师傅一样强就好了（活在梦里

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201129211205.png)

好了下面开始愉快的解题：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201129211442.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201129214520.png)

尝试传入config发现被置为空，if也会被置为空，os也会被置为空。。。。
尝试传入coniffig绕过过滤，失败
尝试传入conosfig绕过过滤，失败
尝试传入iconfigf绕过过滤，成功
仔细想了想觉得就应该直接尝试这种，因为config不怎么使用，而其他的函数优先级都比他高，所以逻辑上来讲应该直接尝试这种。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201129214554.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201129214708.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201129214404.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201129214310.png)

君の名は！

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-732091.png)
