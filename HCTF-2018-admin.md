---
title: '[HCTF 2018]admin'
author: Troy3e
avatar: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/timg.jpg
authorLink: 
authorAbout: 
authorDesc: 
categories: 技术
comments: true
date: 2020-05-27 13:33:56
tags: flask session伪造
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/shjsrjtrj1.jpg
---
进去是一个普通的网站，有登录和注册的功能，在注释里面有提示： you are not admin ，猜想题目是让我们登录成admin，然后出flag，于是想到change password功能，可能可以通过改密码功能的漏洞改掉admin密码，然后以admin登录。于是随便注册一个账号登录，在change password页面发现注释里给出了网站项目的源码：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ图片20200527224826.jpg)
访问查看源码
wp上的源码看起来更清楚，这里就直接搬运一下wp里的源码：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200527225325.png)
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200527225805.png)
   return redirect(url_for('index'))
   return render_template('change.html', title = 'change', form = form)


解法一：flask session 伪造
在Web中，session是认证用户身份的凭证，它具备如下几个特点：
1、用户不可以任意篡改
2、A用户的session无法被B用户获取
也就是说，session的设计目的是为了做用户身份认证。但是，很多情况下，session被用作了别的用途，将产生一些安全问题。
在传统PHP开发中，$_SESSION变量的内容默认会被保存在服务端的一个文件中，通过一个叫“PHPSESSID”的Cookie来区分用户。这类session是“服务端session”，用户看到的只是session的名称（一个随机字符串），其内容保存在服务端。
然而，并不是所有语言都有默认的session存储机制，也不是任何情况下我们都可以向服务器写入文件。所以，很多Web框架都会另辟蹊径，比如Django默认将session存储在数据库中，而对于flask这里并不包含数据库操作的框架，就只能将session存储在cookie中。
因为cookie实际上是存储在客户端（浏览器）中的，所以称之为“客户端session”。

flask的session是存储在客户端cookie中的，而且flask仅仅对数据进行了签名。众所周知的是，签名的作用是防篡改，而无法防止被读取。而flask并没有提供加密操作，所以其session的全部内容都是可以在客户端读取的，这就可能造成一些安全问题。
我们可以用python脚本把flask的session解密出来，但是如果想要加密伪造生成我们自己的session的话，还需要知道flask用来签名的SECRET_KEY，在github源码里找找，可以在config.py里发现下面代码
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200528103425.png)
显然ckj123应该就是SECRET_KEY了
然后就是在github上找一个解密加密的脚本，在解密之后把name中的值换成admin在加密就成功的伪造了session。
步骤如下：
bp抓包获取cookie，通过flask_session_cookie_manager解码，再把name改成admin即可
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200528164916.png)
随后把我们伪造的cookie填上去即可获得flag：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200528165056.png)


还有的解法暂时不去尝试了。

参考链接：
https://www.jianshu.com/p/f92311564ad0
https://www.leavesongs.com/PENETRATION/client-session-security.html
https://www.cnblogs.com/mech/p/12890705.html