---
title: '[CSAWQual 2016]i_got_id'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/534652.jpg'
authorAbout: steamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2020-12-12 22:03:27
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-508247.jpg
---
最近有点划水 补了好多番 今天正好考完六级 定个期末复习间的目标：两天一道题。
还有swpu的题还没复现，先记着吧。

这道题有三个界面
一个helloworld一个注入一个上传

看了一堆人的wp，又是一模一样。越往后做网上的wp质量就越差，基本上都是模仿，没有自己的思考。

本题buu给出了源码，而网上一堆人声称根据页面就猜出源码，如果是大佬那暂且不谈，但猜出来的源码居然也是一个字母不差。。。

首先本题有两个比较陌生的概念。
1、cgi
CGI 是Web 服务器运行时外部程序的规范,按CGI 编写的程序可以扩展服务器功能。CGI 应用程序能与浏览器进行交互,还可通过数据库API 与数据库服务器等外部数据源进行通信,从数据库服务器中获取数据。格式化为HTML文档后，发送给浏览器，也可以将从浏览器获得的数据放到数据库中。几乎所有服务器都支持CGI,可用任何语言编写CGI,包括流行的C、C ++、VB 和Delphi 等。CGI 分为标准CGI 和间接CGI两种。标准CGI 使用命令行参数或环境变量表示服务器的详细请求，服务器与浏览器通信采用标准输入输出方式。间接CGI 又称缓冲CGI,在CGI 程序和CGI 接口之间插入一个缓冲程序，缓冲程序与CGI 接口间用标准输入输出进行通信。
2、perl
一种语言

然后先看上传界面，和网上一个wp中一样，估计一般人都不喜欢注入

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201212231816.png)

源码很简单，没有什么黑名单白名单，那么如果有漏洞的话肯定是函数本身的问题。经过测试发现会返回上传文件的源码。（有的wp说是根据这一点推测出来param()函数以及源码的）。
接着网上查一下param()函数，但是百度上并没有找到利用方法。（懒狗懒得去谷歌）

查阅WP：
param()函数会返回一个列表的文件但是只有第一个文件会被放入到下面的file变量中。而对于下面的读文件逻辑来说，如果我们传入一个ARGV的文件，那么Perl会将传入的参数作为文件名读出来。这样，我们的利用方法就出现了：在正常的上传文件前面加上一个文件上传项ARGV，然后在URL中传入文件路径参数，这样就可以读取任意文件了。

ARGV的具体含义：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201213000252.png)

意思是后面的文件都是第一项（现在是个数组）中的内容

所以burp抓包，url后面加?/flag（这里我是看了github的源码才知道flag在/flag，不知道网上各路大神未作任何说明是啥意思）。同时随便上传一个文件就行

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201213001728.png)

注：content-type这里也是可以随便改的。但是ARGV的那个filename一定给给他去掉，不然就变成传的文件内容是ARGV的，记住这里ARGV是个参数
