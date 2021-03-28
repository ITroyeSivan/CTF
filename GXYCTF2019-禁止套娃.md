---
title: '[GXYCTF2019]禁止套娃'
author: Troy3e
avatar: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg
authorLink: 
authorAbout: 
authorDesc: 
categories: 技术
comments: true
date: 2020-07-09 23:45:04
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-585070.jpg
---
上来是个空白页面，吸取之前的教训，直接dir扫：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200709235122.png)
.git源码泄露
利用githack获取源码
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200710001319.png)
分析源码：
1.需要以GET形式传入一个名为exp的参数。如果满足条件会执行这个exp参数的内容。
2.过滤了常用的几个伪协议，不能以伪协议读取文件。
3.(?R)引用当前表达式，后面加了?递归调用。只能匹配通过无参数的函数。
4.正则匹配掉了et/na/info等关键字，很多函数都用不了。
5.eval($_GET['exp']); 典型的无参数RCE。
geushell不能用了，要想办法读取flag.php的内容，可以用scandir()函数
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200710003431.png)
百度得知，scandir的使用是至少必需要有一个directory的，但是我们又没有办法去定义一个变量，这时候就要考虑php有没有什么函数是自带常量的。
localconv():
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200417145632719.png)
既然是数组，就要用到current()
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200710004304.png)
payload:?exp=print_r(scandir(current(localeconv())));
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200710004905.png)
现在的问题就是怎么读取倒数第二个数组呢？
这里可以用next()函数和array_reverse()函数

next()函数将数组内部指针指向下一个元素
array_reverse()函数将数组倒序

之所以要进行倒序是因为next()里面要是一个数组，连续使用会导致多次使用后括号里不是数组，而进行倒序后再使用next只需使用一次，避开了这个问题。
highlight_file输出，payload：
?exp=highlight_file(next(array_reverse(scandir(current(localeconv())))));


参考链接：
https://blog.csdn.net/weixin_44348894/article/details/105568428
https://www.cnblogs.com/wangtanzhi/p/12260986.html