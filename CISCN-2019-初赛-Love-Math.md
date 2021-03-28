---
title: '[CISCN 2019 初赛]Love Math'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-07-21 21:32:27
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/21313213.jpg
---

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ图片20200721214021.png)

1、长度要小于80
2、不包含黑名单中的字符
3、只含白名单内的字符串/[a-zA-Z_\x7f-\xff][a-zA-Z_0-9\x7f-\xff]*/

首先来提一个概念：php中可以把函数名通过字符串的方式传递给一个变量，然后通过此变量动态调用函数比如下面的代码会执行 system(‘ls’);
$a='system';
$a('ls');

可以尝试构造GET[]然后在传入想用的exp，但是这里把中括号下划线禁用了，那么就得需要编码绕过了。
通过白名单我们看到了一些可能帮助我们编码绕过的函数 base_convert ，dechex，第一个可以进行进制之间的转换，比如 base_convert("1001"2,10)是将二进制的1001转换为10进制，第二个函数是将10进制转成16进制。
payload：?c=$_GET[a]($_GET[b])&a=system&b=cat falg
中括号被禁用我们可以用花括号代替{}，要构造的字符串为 _GET,php中有将16进制转成字符串的函数hex2bin，那么hex2bin又怎么生成呢，这时就需要我们的base_convert函数了，36进制也就是base36中有字母数字正好可以满足。
所以 base_convert(37907361743,10,36)=hex2bin。因为我们是要得到_GET所以就得用到另外一个函数dechex将_GET的10进制转为16进制再通过hex2bin转换为字符串
所以_GET=base_convert(37907361743,10,36)(dechex(1598506324));
既然已经可以构造出$_GET[]了，那么直接将上面的payload修改下即可

c=$pi=base_convert(37907361743,10,36)(dechex(1598506324));$$pi{pi}($$pi{abs})&pi=system&abs=cat /flag
