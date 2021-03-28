---
title: BJDCTF2nd-web
author: Troye
avatar: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20160708223941_YQncj.jpeg
authorLink: 
authorAbout: 
authorDesc: 
categories: 技术
comments: true
date: 2020-03-29 11:16:18
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/timg (2).jpg
---
1、fake google
输入什么就会回显什么，在注释里看到给的提示：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200329111758.png)
百度可知是SSTI（模板注入），没接触过所以首先学习一下这方面的知识：和常见Web注入的成因一样，也是服务端接收了用户的输入，将其作为 Web 应用模板内容的一部分，在进行目标编译渲染的过程中，执行了用户插入的恶意内容。
输入{{‘a’+’x’}}发现回显为ax，说明存在模板注入
输入：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200329114126.png)
来查看根目录，发现flag在根目录里：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/2321.png)
接下来把ls换成cat /flag就可得到flag：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/2333.png)
另一种比较方便的方法是用tplmap，傻瓜式工具还是比手工香：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/1.jpg)
2、old hack
没发现什么有价值的信息，除了告诉我们用了TP5
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/66.png)
想办法构造一个ThinkPHP的报错看一下详细版本：
http://d701e9e5-05ba-44f3-9d85-a419b028ef7b.node3.buuoj.cn/?s=1
（“t框架有s参数可以加载模块”，不知道这句话什么意思，不过我试了下换成别的字母确实不会报错。）
报错后看到了thinkphp的详细版本：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200329113032.png)
然后就是百度搜一个这个版本的执行漏洞就行了
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200329113058.png)
3、duangshell
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200329113127.png)
swp显然是提示了，以前遇到过此类题目，是vim非正常关闭时会产生一个swp文件。这里先下载备份文件url/.index.php.swp，然后用vim恢复：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/555.png)
一开始看到这里还觉得蛮简单，后来看了wp才知道这里要用到反弹shell的操作。因为exec()不会回显，反弹shell能够解决而且又没有过滤curl。可能是靶机不能访问外网，我的虚拟机上一直没有回显。应该是要在内网开一台虚拟机再操作的，这里就直接放上模仿的师傅wp了。
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/8.png)
