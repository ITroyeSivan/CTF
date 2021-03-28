---
title: '[CISCN2019 华北赛区 Day1 Web2]ikun'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-07-22 21:18:34
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/hrthrtwhnrtjn.png
---
很有趣的一道题目。。。
注释里说留了些提示，大致翻了下也没找到有用的信息。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200722212930.png)

注册了一个账号，自己买了几个b站号后突然注意到一个小细节，说一定要买到lv6。
但是翻了四页没有找到六级账号，有意思，突破口应该就在这里了。
手动应该是不行的了，于是写脚本来检索六级账号：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ图片20200722213842.png)

查看180页，找到了6级账号和flag提示，但是买不起：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200722214029.png)

抓包修改下折扣试试看

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200722220257.png)

看到了后台的地址，提示只允许admin访问。

这时注意到有jwt签名。（了解jwt：https://www.cnblogs.com/cjsblog/p/9277677.html）

伪造jwt

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200722222242.png)

将后面解码不出来的通过c-jwt-crack爆破一下密钥

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200722223241.png)

然后伪造我们的jwt（https://jwt.io/）

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200722223915.png)

看到了下一步的提示（居然还没结束。。。）

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200722223953.png)

查看www.zip中的内容（下面不会了，看的wp），注意到Admin.py文件。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200722224401.png)

这里的become先进行一次url解码，再进行pickle反序列化
构造一下pickle反序列化
python魔法方法指南：https://blog.csdn.net/bluehawksky/article/details/79027055

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200722230931.png)

得到flag。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ图片20200722231143.png)



