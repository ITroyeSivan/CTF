---
title: Bugku-cookies欺骗
author: Troy3e
avatar: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg
authorLink: steamID：888007034
authorAbout: Blizzard：TroyeSivan#51769
authorDesc: 
categories: 技术
comments: true
date: 2020-07-13 23:12:11
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/vbyhuidsvosiybvu.jpg
---
进去是一堆乱码：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200713233342.png)
注意到url中filename有点像base64，解码得到keys.txt，猜测filename参数会决定所返回得页面。
尝试访问index.php。filename=aW5kZXgucGhw。没有任何内容，此时注意到前面还有个line，猜测是回显的行数，输入1得到error_reporting(0); ，输入2得到$file=base64_decode(isset($_GET['filename'])?$_GET['filename']:""); 
可以确定line就是返回的行数了，于是写个脚本来直接得到源码：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200713232654.png)
关于requests.Session()：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200713234313.png)
后面就比较简单了，因为源码并不复杂，抓包后加上cookie：margin=margin;