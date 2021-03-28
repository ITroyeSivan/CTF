---
title: SUCTF 2019-CheckIn
author: Troy3e
avatar: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg
authorLink: 
authorAbout: 
authorDesc:
categories: 技术
comments: true
date: 2020-05-29 16:15:37
tags: upload
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/385bd477f37b3a5cfec21c9bf00f76129e67a0e8.jpg@1320w_742h.jpg
---
经过几次简单的测试可以发现：后台是通过exif_imagetype函数判断图像的类型的，且文件中不能包含<?。
这里新了解到了一种方法-.user.ini：
首先介绍php.ini文件，php有很多配置，并可以在php.ini中设置。在每个正规的网站里，都会由这样一个文件，而且每次运行PHP文件时，都会去读取这个配置文件，来设置PHP的相关规则。
这些配置可以分为四种：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/2014103002272568560.jpg)
我感觉是按重要程度分类了，比如关乎到系统一类的配置，那一类的全部配置，都属于“PHP_INI_SYSTEM”。它只能在，像php.ini这样的“厉害”的文件里可以设定。而其他的三类不怎么重要的配置，除了可以在php.ini中设定外，还可以在其它类似的文件中设定，其中就包括.user.ini文件。

实际上，除了PHP_INI_SYSTEM以外的模式（包括PHP_INI_ALL）都是可以通过.user.ini来设置的。而且，和php.ini不同的是，.user.ini是一个能被动态加载的ini文件。也就是说我修改了.user.ini后，不需要重启服务器中间件，只需要等待user_ini.cache_ttl所设置的时间（默认为300秒），即可被重新加载。

这里就很清楚了，.user.ini实际上就是一个可以由用户“自定义”的php.ini，我们能够自定义的设置是模式为“PHP_INI_PERDIR 、 PHP_INI_USER”的设置。（上面表格中没有提到的PHP_INI_PERDIR也可以在.user.ini中设置）

其中有两个配置，可以用来制造后门：
auto_append_file、auto_prepend_file
指定一个文件，自动包含在要执行的文件前，类似于在文件前调用了require()函数。而auto_append_file类似，只是在文件后面包含。 使用方法很简单，直接写在.user.ini中：

auto_prepend_file=test.jpg

那么当我们访问此目录下的任何一个文件时，都会去包含test.jpg

因为后台用exif_imagetype函数检测文件类型，所以我们在文件前加上图片的特征，来绕过检测。
GIF89a                  
auto_prepend_file=a.jpg 
随后制作一个图片马或者分别上传.user.ini和a.jpg都行
最后在网页端执行：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/fyubwifgyubgiouwrg.jpg)



参考链接：
https://wooyun.js.org/drops/user.ini%E6%96%87%E4%BB%B6%E6%9E%84%E6%88%90%E7%9A%84PHP%E5%90%8E%E9%97%A8.html
https://blog.csdn.net/weixin_44077544/article/details/102688564
