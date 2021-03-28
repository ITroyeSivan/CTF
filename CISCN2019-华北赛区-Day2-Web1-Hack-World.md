---
title: '[CISCN2019 华北赛区 Day2 Web1]Hack World'
author: Troy3e
avatar: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg
authorLink: 
authorAbout: 
authorDesc: 
categories: 技术
comments: true
date: 2020-07-03 14:02:01
tags: sql 异或注入
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/earheaheah.jpg
---
暑假第一次更博，目标每天至少buu五道题和两篇博客。
这道题主要是想记录一下自己第一次尝试写的脚本
可以用异或注入。
简单介绍下原理：
当两边相同的时候，输出值为0，如1^1=0，
不同的时候，输出值为1，如1^0=1。
于是可以用脚本一位一位判断过去。
第一个自己写的脚本（其实是先看了一遍思路然后再写的）：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200703160807.png)
有几个注意点：
1、用括号或者tab都可以代替空格
2、网站用了waf所以有刷新时间限制，建议加一个sleep函数，我第一次的脚本可能就是因为这个原因跑出了错误的结果。
