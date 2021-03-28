---
title: '[NPUCTF2020]ezlogin'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-11-04 13:03:50
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1111062.png
---
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/8738.jpg)

一个登陆界面，没有扫到任何东西。
session每隔几秒就会失效，每次登录只能使用一次。否则就会显示超时。

尝试万能密码但是没用。

这里要用到XPath注入：
https://xz.aliyun.com/t/7791
https://www.cnblogs.com/backlion/p/8554749.html
XPath 即为 XML 路径语言，是 W3C XSLT 标准的主要元素，它是一种用来确定 XML（标准通用标记语言的子集）文档中某部分位置的语言。
XPath 基于 XML 的树状结构，有不同类型的节点，包括元素节点，属性节点和文本节点，提供在数据结构树中找寻节点的能力，可用来在 XML 文档中对元素和属性进行遍历。
XPath 使用路径表达式来选取 XML 文档中的节点或者节点集。这些路径表达式和我们在常规的电脑文件系统中看到的表达式非常相似。
XPath是一种用来在内存中导航整个XML树的语言,它的设计初衷是作为一种面向XSLT和XPointer的语言,后来独立成了一种W3C标准。

本来想细写一下的又被buu气到了。。。
buu的429让很多脚本都失效了 搞了半天一直报错心态都崩了 说什么数组超出范围？

加了个时间函数搞定。

XPathpayload 上面链接都有。