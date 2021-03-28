---
title: '[GoogleCTF2019 Quals]Bnv'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-10-24 21:34:35
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-134060.jpg
---
[GoogleCTF2019 Quals]Bnv-xxe

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/0LC%5BCY1J%601WP5I@@UC6Z_00.png)

让我们选一个离我们最近的搜索引擎

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201023102153.png)

抓包发现采用JSON进行数据传输，可能存在xxe。
但是在此之前 ，先重新学习一下基础知识：https://blog.csdn.net/qq_36119192/article/details/83047334

XML(Extensible Markup Language)扩展标记语言 ，是一种常用的标记语言，用于标记电子文件使其具有结构性，可以用来标记数据、定义数据类型，是一种允许用户对自己的标记语言进行定义的源语言。 XML使用 DTD(document type definition)文档类型定义来组织数据；格式统一，跨平台和语言，早已成为业界公认的标准。XML是标准通用标记语言 (SGML) 的子集，非常适合 Web 传输。XML 提供统一的方法来描述和交换独立于应用程序或供应商的结构化数据。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201023111114.png)

DTD(文档类型定义)
DTD（文档类型定义）的作用是定义 XML 文档的合法构建模块。
DTD 可以在 XML 文档内声明，也可以外部引用。

1：内部声明：<!DOCTYPE   根元素   [元素声明]  > 

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201023112800.png)

2：外部声明（引用外部DTD）：<!DOCTYPE 根元素 SYSTEM "文件名">  

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201023112915.png)

内外部实体又可分为 一般实体 和 参数实体

1、一般实体的声明语法：<!ENTITY  实体名   "实体内容“>                  引用实体的方式：&实体名；
2、参数实体只能在DTD中使用，参数实体的声明格式： <!ENTITY  %实体名   "实体内容“>        引用实体的方式：%实体名；

而且参数实体还能嵌套定义，但需要注意的是，内层的定义的参数实体% 需要进行HTML转义，否则会出现解析错误。

XXE常见利用方式

与SQL相似，XXE漏洞也分为有回显和无回显
有回显，可以直接在页面中看到payload的执行结果或现象。
无回显，又称为blind xxe，可以使用外带数据(OOB)通道提取数据。即可以引用远程服务器上的XML文件读取文件。

解题

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201023131307.png)

直接转化为xml形式会提示没有找到DTD，所以需要在DTD中添加一个简单的实体。添加了简单实体后会说没有message元素的声明。加上之后就如上图所示，返回成功，我们得到了正确的响应。

接下来添加一个外部实体来发出出口请求。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201023132321.png)

我们得到一个标记错误。这意味着文件已经正确加载了，但由于它不是个格式良好的xml文件 所以它中断了。现在我们有了一种枚举文件名的方法，让我们看看是否可以找到一个标志文件。
读取/flag文件也是相同回显，这意味着标志就存在路由中，然后被标记为一个文件。我们所要做的就是以某种方式来读取它。但是怎么读取呢？

Blind-XXE 引用本地DTD文件：http://yugod.xmutsec.com/index.php/2019/07/14/50.html

如果目标主机的防火墙十分严格,不允许我们请求外网服务器dtd,那么我们可以通过引入本地dtd文件实现XXE。

ubuntu系统自带/usr/share/yelp/dtd/docbookx.dtd文件

它定义了很多参数实体并调用,所以我们可以在内部重写一个该dtd文件中含有的参数实体,如ISOmaso

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201023132651.png)

我们在响应中得到的文件名与错误的文件名相同，这是可以被滥用的。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201023133326.png)
首先我们读取了所需文件的内容，它可以是一个/flag，它也可以使/etc/password，然后我们可以尝试读取另一份文件，但是我们要确保第二个是个假文件名是我们刚刚读取第一份文件的内容，显然这会给我们一个错误，因为没有文件名作为第一个文件的内容，在错误中我们得到了文件的名称，我们尝试阅读那些意味着，我们也会取回第一个文件的内容，因此使用本地DTD，通过XXE读取任意文件。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201023134844.png)

晕乎乎的，看了一堆文章，不知道自己有没有搞懂。。。
上面都是网上一些文章的解读，感觉没有一个讲明白的。
我自己的理解是这样，首先这是一个无回显Blind XXE。由于目标主机不允许我们请求外网服务器dtd，那么我们只能借用本地的dtd文件，就像这题里面的/usr/share/yelp/dtd/docbookx.dtd（不知道是咋找到的？应该是根据系统推断的？），重写这个dtd文件里面的参数实体使之满足我们的目的，即此题中的ISOamso。首先我们读取/flag文件，该文件已经被正确加载但由于它不是个格式良好的xml文件，所以它中断了。flag的内容被保存到file变量。flag的内容被保存到file变量。flag的内容被保存到file变量。重要的事情说三遍。通过访问不存在的文件报错带出即可。
就因为不清楚flag内容一开始去哪了卡了一小时没懂。。。。。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201023174126.jpg)

溜了溜了。
