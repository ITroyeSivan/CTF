---
title: '[0CTF 2016]piapiapia'
author: Troy3e
avatar: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg)
authorLink: 
authorAbout: 
authorDesc: 
categories: 
comments: true
date: 2020-07-08 22:05:06
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-789452.jpg
---
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200708221232.png)
一个很普通的登陆界面，试了sql发现不行，找了半天也没找到线索，dir扫也没有结果。但是看了wp里面都是用的dirsearch扫出了源码，网上搜了下原因发现有时候在后面添加个延时参数，会大大提高扫描效率。
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200708223439.png)
（通过此题，我发誓以后不管怎样一定要先扫一遍）
查看源码：
config.php里有flag，但不可读：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200708232419.png)
profile.php里出现了反序列化：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ图片20200708232621.png)
下面有一个读取文件的函数猜测可以利用，只要将$profile['photo']的值改为config.php，就能读取到config.php里面的flag
在源码里整理一下逻辑结构
register->login->update->profile
登录和注册不看，从update开始
对各个参数进行一些过滤，然后序列化$profile，跟进update_profile
前面已经知道，我们的目的是要读取config.php从而得到flag，读取config.php需要替换$profile[‘photo’]，也就是要让config,php成为序列化的一部分，可以利用的是反序列化字符串逃逸。
在后端中，反序列化是以";}结束的，因此如果我们把";}带入需要反序列化的字符串中（除了结尾处），就能让反序列化提前结束而后面的内容就会被丢弃
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200709000018.png)
可知此处profile序列化后的结果为：
a:4:{s:5:"phone";s:11:"12345678900";s:5:"email";s:12:"123@qq.com";s:8:"nickname";s:3:"123";s:5:"photo";s:39:"upload/07cc694b9b3fc636710fa08b6922c42b";}
（此处为我刚刚注册的信息）
然后我们将config.php替换进去：
a:4:{s:5:"phone";s:11:"12345678900";s:5:"email";s:12:"123@qq.com";s:8:"nickname";s:3:"123";s:5:"photo";s:10:"config.php";}s:39:"upload/07cc694b9b3fc636710fa08b6922c42b";}
虽然给他反序列化之后结果photo的部分是config,php，但这样是读不到config.php的，因为更新profile的时候根本没地方插进去，因此就需要从nickname入手把这些数据悄悄带进去
首先解决nickname的长度限制问题
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200709000915.png)
直接将nickname变成数组就可突破限制
a:4:{s:5:"phone";s:11:"12345678900";s:5:"email";s:12:"123@qq.com";s:8:"nickname";a:1:{i:0;s:3:"123";}s:5:"photo";s:10:"config.php";}s:39:"upload/07cc694b9b3fc636710fa08b6922c42b";}
现在我们考虑怎么让";}s:5:“photo”;s:10:“config.php”;}这34个字符逃逸出来
前面提到Fliter会将where一类的函数替换成hacker，也就是说where在被正则替换后，其本身的长度会加1，如果我们构造34个where
那么在传入后端之后hacker的长度就会将我们目标逃逸字符挤掉
过程如下
传入:
s:8:"nickname";a:1:{i:0;s:204:"34*where";}s:5:"photo";s:10:"config.php";}

此时34*where";}s:5:"photo";s:10:"config.php";}都作为nickname存在

正则替换:
s:8:"nickname";a:1:{i:0;s:204:"34*hacker";}s:5:"photo";s:10:"config.php";}

因为s只有204个字符,所以读取第34个hacker之后就停止,34个字符";}s:5:"photo";s:10:"config.php";}不再包含在nickname内

既然从nickname逃逸出，"};将前面的nickname数组闭合之后，剩下的s:5:"photo";s:10:"config.php";}就会被当作photo的部分了，至于后面的upload，由于被后面";}结束反序列化，也就被丢弃，这样就实现了config.php的读取
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ图片20200709002816.png)
然后查看profile.php
查看图片源码，base64解码即可：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200709002924.png)

总结：这道题应该是目前为止做过的最复杂的一题了，过几天自己再去复现一遍。

参考链接：https://blog.csdn.net/qq_43756333/article/details/106420509?utm_medium=distribute.pc_relevant.none-task-blog-baidujs-6



