---
title: '[V&N2020 公开赛]HappyCTFd'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-08-04 21:54:12
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/5346636.jpg
---
要梯子才能打开。
CTFd平台，给我一种要发现0day的幻觉QWQ。。。
源码没有任何提示，于是尝试注册一个账号。
没有题目，但是在用户界面发现了admin账号，应该是突破口了。
网上查了一下应该是之前CTFd修复的账号接管漏洞。
https://www.colabug.com/2020/0204/6940556/amp/

1、注册
首先申请一个内网的邮箱，因为靶机没法访问外网，在https://buuoj.cn/resources中找到相应链接。
然后注册账户，空格绕过：（空格）admin

2.忘记密码，拿到admin的密码
注册好以后退出登录，用admin登录（不加空格），点击忘记密码并将邮件发送到刚刚注册的内网邮箱，重置密码。

3、flag
用admin账号登录，在hidden的misc题目附件中找到flag

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200804223905.png)

这道题其实没学到啥，只是觉得好玩。。
