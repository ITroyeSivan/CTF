---
title: N1CTF WP
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-10-19 20:38:28
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-488291.jpg
---
N1CTF WP
前言
差不多算是自己第一次认真的打比赛了，收获很大，见识了一堆巨佬。关于做题方面，终于不算是签到选手了。磨了七八个小时，欠了米神二十顿打才磨出了Web的SignIn。总体来说还是非常开心(?)的。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201019135009.jpg)

Web-SignIn

进去就是源码

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/3858458.jpg)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/64567456.jpg)

反序列化，首先寻找flag的输出点：在flag类的getflag函数里，当check对象的值等于key时读取flag文件。

这个key没有任何的信息，所以肯定是要通过sql注入爆出来的。

那么我们第一步便是要通过sql注入爆出key。然而单看ip类，tostring魔法函数不仅没法触发，就算触发了也不会输出报错信息。由此我们去flag类里寻找方法。

wakeup魔法函数中对ip对象有一个判断并且有不同的回显。如果ip中有n1ctf则另ip为"welcome to n1ctf2020"并通过destruct函数进行输出。
但这怎么利用呢？反观上面ip类的tostring函数，如果sql语句执行成功，则返回"your ip looks ok!"，不成功的话则返回报错信息。
试想，如果让flag类的ip实例化为ip类，通过报错注入返回n1ctf，并且通过destruct有一个回显。那么是不是就实现了盲注了呢？

思路还是很清晰的，但自己动起手来还是有一堆问题。。。

首先就是这个tostring魔法函数的触发，其实知识点都知道，一个特别简单明显的触发，但当时也不知道咋回事就是没想通。应该是感冒一直咳嗽影响我QWQ（害敢找理由？你就是菜，爬。
对象和字符串的转化都会触发tostring，之前遇到好多次了，呜呜呜，可能比赛的时候脑子短路了。（爬！

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/442.jpg)

if语句里就有一个转换，可以触发tostring，那么直接在construct函数里让ip对象实例化为ip类就行了。
pop链如下：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201019142650.png)

这样就完成了第一步。接下来就是报错注入了。我们要让sql语句返回默认的字符串'n1ctf'，yysy这我真没想到能用exp()。这应该是深入理解sql语句的人才能想到的吧（米神yyds），我做的还是太少了。
在本地进行测试：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201019143611.png)

简单解读一下，0=1时这个值永假不执行，它的值就为null。
1=1时永真，但在执行后面那个exp(~'n1ctf')的时候由于这个函数本身问题，就会报错。

insert into information(xx,xx) values('1' and x=1 and exp(~'n1ctf'),'pig1');
当x=1的时候会返回n1ctf，当x=0的时候语句成功执行。

在理解了这些之后，二分法脚本就可以启动了。
脚本如下：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201019144644.png)

自己写的有几个小问题记录一下（挨打
1、判断的时候一开始直接写的if "welcome to n1ctf2020" in r.text.....这个写快了没想到，这串字符就在源码里面。。。。
2、headers{"X-Forwarded-For":xff1}这里面xff1不必再加引号，这个确实是个细节上的失误。
3、还有个啥写外面了那个应该问题不大，只是忘改了。

爆出key之后，令check等于key提交即可获得flag。


