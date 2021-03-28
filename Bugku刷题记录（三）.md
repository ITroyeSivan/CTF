---
title: Bugku刷题记录（三）
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-07-16 17:38:55
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1088161.jpg
---
一、PHP_encrypt_1(ISCCCTF)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ图片20200716191725.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200716191800.png)

传入flag作为参数给data得到了题目中那一串字符。附件中的代码给出了加密函数，所以只要写出泄密脚本就可以得到flag。

二、文件包含
挂了。

三、flag.php
点login没有任何反应，提示是hint，一开始以为是打错了或者应该漏了些什么，没想到是在提示hint是个参数。。。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200716194141.png)

得到源码。大致一看会觉得很简单，只要把ISecer:www.isecer.com序列化一下，然后bp提交cookie就行，其实仔细看下会发现，验证cookie的时候$KEY根本没有定义。。。。所以只要提交一个空字符串就行。cookie:ISecer=s:0:"";

四、sql注入2
这道题大概做了两个小时。。。。
确实过滤了很多东西。新学习了一个减号闭合。还白嫖了一个很棒的字典QAQ。
先来介绍一下减号闭合。
如果输入admin'-1-'，那么原语句就是select 'admin'-0-'';
会发生类型转换，字符串变成0，所以就是0-0-0=0
又因为select * from users where name=0 ,会输出所有语句。
（$sql = select * from users where username=$username;
在字符串username的值和数字0比较的时候，字符串变为了0
故此0=0）
所以可以利用这点进行注入。
substr、空格都被过滤了，但这里还可以用mid和括号来代替。
构造语句
ascii(substr((select database()),1,1))>1
很多过滤了，这个语句没法使用
用到一个倒着截取
假设：
passwd=abc123
那么我们用以下方式
   mid((passwd)from(-1)):3
   mid((passwd)from(-2)):23
   mid((passwd)from(-3):123
倒着看的第一位都是3，显然不行，无法截取出来，于是想到反转
先反转
REVERSE(MID((passwd)from(-%d))
再取最后一位
mid(REVERSE(MID((passwd)from(%-d)))from(-1))
在比较ASCII
ascii(mid(REVERSE(MID((passwd)from(%-d)))from(-1)))>1
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200716224339.png)

后面得到了密码登陆进去就比较简单了，但这sql做的我心态大崩。。。

bugku就先做到这里，看到后面一题是要用到渗透测试第一步信息收集。。
回去接着做buu