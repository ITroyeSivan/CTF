---
title: '[网鼎杯 2018]Comment'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-07-29 22:35:01
authorLink:
tags: Git恢复+二次注入
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-593325.jpg
---
一个留言板，留言要登陆，提示了zhangwei、zhangwei***，burp爆破得密码zhangwei666。
直接dir扫，git源码泄露：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200729223948.png)

GItHack启动（之前GitHack报错是因为忘记把python3切换成python2了，属实憨憨）：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ图片20200729224203.jpg)

到这里卡住了，因为这个实在看不出来啥，看了wp才知道这是一段残缺的代码，需要进行恢复，又涨知识了QAQ。

指令：git log --reflog

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200729224523.png)

接下来：git reset --hard xxxxx
但这里环境好像出了点问题，不知道什么原因，一直没有成功恢复，估计不是我的问题了，应该是题目的问题。
假装恢复了源码：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200729230344.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200729232616.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200729234737.png)

可以发现当do=write的时候，传入的信息都会进行转义，但是数据库会自动清除反斜杠，

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200729234747.png)

当do=comment的时候，可以发现直接从category这个字段进行查询，这就导致了二次注入
所以说那个转义函数根本起不到防护的作用

二次注入原理：https://www.jianshu.com/p/3fe7904683ac
二次注入可以理解为，攻击者构造的恶意数据存储在数据库后，恶意数据被读取并进入到SQL查询语句所导致的注入。防御者可能在用户输入恶意数据时对其中的特殊字符进行了转义处理，但在恶意数据插入到数据库时被处理的数据又被还原并存储在数据库中，当Web程序调用存储在数据库中的恶意数据并执行SQL查询时，就发生了SQL二次注入。

可以发现content这个变量最后会回显在我们的留言页面中，那么就通过他来输出我们的sql语句
然后闭合sql语句 由于内容太多了，采取/**/的批量注释方法,

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200730002906.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200730003106.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200730003130.png)

接着',content=(select(load_file("/etc/passwd"))),/*

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200730003431.png)

注意看到/home/www下以bash身份运行
',content=(select(load_file("/home/www/.bash_history"))),/*

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200730003528.png)

看见他删除了 .DS_Store 文件，由于目标环境是docker，所以 .DS_Store 文件应该在 /tmp/html 中。
',content=(select(load_file("/tmp/html/.DS_Store"))),/*

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200730003621.png)

未显示完全,用hex编码显示
',content=(select hex(load_file("/tmp/html/.DS_Store"))),/*

解码得

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200730004005.png)

payload：213',content=(select hex(load_file('/var/www/html/flag_8946e1ff1ee3e40f.php'))),/*

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200730004216.png)

解码得flag。

总结：这道题目还是挺迷的。。。第一个是直接user()而不是database()。后面二次注入虽然懂了原理但是还是不知道为什么会想到这样做。不知道自己什么时候能完全独立解出这种题。



