---
title: LCTF2018-bestphp's revenge
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-09-11 21:01:35
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1099559.jpg
---
这题差点做睡着了，太xx难了，想了好久不知道咋下手，看wp了。
源码

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200911211023.png)

还有一个flag.php（其实这flag.php我都不知道怎么找到的，难道比赛的时候有提示?）

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200911211049.png)

通过flag.php可以看出ssrf。
既然是SSRF，那么该如何满足以下条件呢？
访问127.0.0.1/flag.php
cookie可控，改成我们的php_session_id
那么势必得到一个php内置类，同时其具备SSRF的能力。

1、php内置类SoapClient
SOAP是webService三要素（SOAP、WSDL(WebServicesDescriptionLanguage)、UDDI(UniversalDescriptionDiscovery andIntegration)）之一：WSDL 用来描述如何访问具体的接口， UDDI用来管理，分发，查询webService ，SOAP（简单对象访问协议）是连接或Web服务或客户端和Web服务之间的接口。

其采用HTTP作为底层通讯协议，XML作为数据传送的格式。简单来讲，我们可以通过它来发送http/https请求。
SoapClient类可以创建soap数据报文，与wsdl接口进行交互。
简单的用法：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200911231405.png)


2、CRLF Injection漏洞
CRLF是”回车+换行”（\r\n）的简称。在HTTP协议中，HTTPHeader与HTTPBody是用两个CRLF分隔的，浏览器就是根据这两个CRLF来取出HTTP内容并显示出来。所以，一旦我们能够控制HTTP消息头中的字符，注入一些恶意的换行，这样我们就能注入一些会话Cookie或者HTML代码，所以CRLFInjection又叫HTTPResponseSplitting，简称HRS。
简单来说
http请求遇到两个\r\n即%0d%0a，会将前半部分当做头部解析，而将剩下的部分当做体，当我们可以控制User-Agent的值时，头部可控，就可以注入crlf实现修改http请求包。


但是怎么触发反序列化呢?

3：serialize_hander处理session方式不同导致session注入
看到

if(isset($_GET[name])){
  $_SESSION[name] = $_GET[name];
}

我们不难想到，可以将序列化内容通过$_GET[name]传入session。

首先我们可以控制session.serialize_handler,通过

/?f=session_start

serialize_handler=php

这样的方式，可以指定php序列化引擎,而不同引擎存储的方式也不同

php_binary:存储方式是，键名的长度对应的ASCII字符+键名+经过serialize()函数序列化处理的值

php:存储方式是，键名+竖线+经过serialize()函数序列处理的值

php_serialize(php>5.5.4):存储方式是，经过serialize()函数序列化处理的值

同时根据文章内的内容，当session反序列化和序列化时候使用不同引擎的时候，即可触发漏洞

假如我们使用php_serialize引擎时进行数据存储时的序列化，可以得到内容

$_SESSION[‘name’] = ‘sky’;
a:1:{s:4:”name”;s:3:”sky”;}

而在php引擎时进行数据存储时的序列化，可以得到另一个内容

$_SESSION[‘name’] = ‘sky’;
name|s:3:”sky”

那么如果我们用php引擎去解php_serialize得到的序列化，是不是就会有问题了呢？

答案是肯定的，该文章中也介绍的很清楚

php引擎会以|作为作为key和value的分隔符，我们再传入内容的时候，比如传入

$_SESSION[‘name’] = ‘|sky‘

那么使用php_serialize引擎时可以得到序列化内容

a:1:{s:4:”name”;s:4:”|sky”;}

然后用php引擎反序列化时，|被当做分隔符，于是

a:1:{s:4:”name”;s:4:”

被当作key

sky

被当做vaule进行反序列化

于是，我们只要传入

$_SESSION[‘name’] = |序列化内容



为什么能反序列化？

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200911234959.png)

光进行反序列化肯定是不够的
我们看到soapclient想要触发__call()必须要调用不可访问的方法，那我们如何在题目有限的代码里调用不可访问方法呢？

看到这段代码

php $a = array(reset($_SESSION),'welcome_to_the_lctf2018'); call_user_func($b,$a);

这里只要覆盖$b为call_user_func即可成功触发不可访问方法。

最后总结一下思路：
根据flag.php想到ssrf，所以要想办法触发ssrf，利用回调函数覆盖session序列化引擎为php_serilaze，构造SSRF的Soap类的序列化字符串配合序列化注入写入session文件，然后利用变量覆盖漏洞，覆盖掉变量b为回调函数call_user_func，此时结合回调函数调用Soap类的未知方法，触发__call方法进行SSRF访问flag.php。把flag写入session，再把session打印出来即可。


接下来开始解题。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200912104140.png)

第一次发包：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/745637.jpg)

记住这里面的cookie不能用自己的，否则会504，原因不明。

第二次发包：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200912104853.png)

第三次发包：
用虚构的cookie替换自己的cookie访问：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200912105120.png)

第一次见到这种类型的，人傻了。
