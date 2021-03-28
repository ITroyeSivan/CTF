---
title: '[CISCN2019 华北赛区 Day1 Web1]Dropbox'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-07-23 21:27:58
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/124141.jpg
---
一道题一晚上，心 态 崩 了
进去又是个登录框，遇事不决先dir扫一遍，可惜没什么发现：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/546456.jpg)

然后注册了一个账号并登陆了进去，看到有个上传文件，传张图片试试，可以下载：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ图片20200723222453.jpg)

抓包试试：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200723222608.png)

可以看到filename，猜测可以利用这个点来下载任意文件。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200723223247.png)

成功查到源码

然后用相同办法下载所有的源码，index.php，upload.php, register.php, login.php, download.php, delete.php，再加上这些php文件中include的class.php。

然后，然后就不会了。。。

后面直接复制大佬的wp了：

File类中的close方法会获取文件内容，如果能触发该方法，就有可能获取flag。

User类中存在close方法，并且该方法在对象销毁时执行。

同时FileList类中存在call魔术方法，并且类没有close方法。如果一个Filelist对象调用了close()方法，根据call方法的代码可以知道，文件的close方法会被执行，就可能拿到flag。

根据以上三条线索，梳理一下可以得出结论:

如果能创建一个user的对象，其db变量是一个FileList对象，对象中的文件名为flag的位置。这样的话，当user对象销毁时，db变量的close方法被执行；而db变量没有close方法，这样就会触发call魔术方法，进而变成了执行File对象的close方法。通过分析FileList类的析构方法可以知道，close方法执行后存在results变量里的结果会加入到table变量中被打印出来，也就是flag会被打印出来。

想实现上述想法，可以借助phar的伪协议。

生成phar文件后在删除的时候进行触发即可得到flag。

关于phar伪协议的用法，今天就不细细研究了，太晚了。后面几天整理知识点的时候再看看。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200724010041.png)

exp:

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200724010103.png)

记得在php.ini配置文件里面修改phar.readonly为Off。（并且把前面分号去掉QAQ）
将生成的文件上传
bp抓包改类型为image/gif
之后删除
抓包修改为filename=phar://test.gif
flag在response里

总结：我只能说，tql