---
title: 用docker搭建CTFd及近期拖更情况说明
author: Troy3e
avatar: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg
authorLink: 
authorAbout: 
authorDesc: 
categories: 技术
comments: true
date: 2020-07-13 12:34:21
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-710137.png
---
前几天一直在搞科一模拟（要连续两次95+QAQ），考了两天终于过了，17号正式考试。至于托更，一方面是因为在搞科一，另一方面是因为buu上现在做到的题确实太难了，感觉自己的基础并没有扎实到可以去做那些题目，所以打算接下来去把bugku上的基础题刷完（今天开始，这周的任务会尽量完成）。在考科一的空闲时间里我学习了如何搭建一个自己的CTF平台，接下来就是搭建的过程及踩的坑。
首先是想在虚拟机上先本地搭建一个测试一下的，但是到了最后安装的时候，各种报错，几乎所有问题的种类都遇到了一遍，一晚上一直在研究玄学问题，到最后感觉虚拟机都已经废了。于是换了一台虚拟机继续，但还是同样的问题，应该是网上的教程比较老，一些改动没有加进去，比如说有些源根本就无法连接。
到这差不多半天没了，如果这样还不行的话，只能换一种办法了——docker。
用docker的话，我想一步到位了，直接在服务器上搭建。
一、申请阿里云服务器
通过阿里云的学生认证后可以免费领取一个云服务器，最好选择Linux的系统，不要问为什么不选Windows的，问就是花了两小时踩坑，ssh连不上，不知道是不是openssh服务没有安装的原因。在都设置好之后（如何设置可参考：https://www.cnblogs.com/Guorisy/p/12445224.html），用xshell连接上去。
二、开始搭建
使用xshell连接服务器后，在终端输入sudo vim /etc/apt/sources.list，将里面的内容删除换成：
//添加阿里源
//deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
//deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
//deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
//deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
//deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
//deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
//deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
//deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
//deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
//deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
替换完成保存
然后执行命令
sudo apt-get update
sudo apt-get upgrade #更新所有软件
注意这里更新后会弹出选项，非常重要，选不好就gg了
无脑选N
好了，更新源后，一键安装docker：wget -qO- https://get.docker.com/ | sh
然后在安装docker-compose：pip install docker-compose
接下来安装CTFd：
搜索镜像
docker search CTFD
拉取镜像
docker pull ctfd/ctfd
运行CTFd
docker run -d -p 8080:8000 ctfd/ctfd
然后访问服务器ip即可。
顺便说一句，记得开放对应端口。


接下来会学习并进行深度自定义及上传题目。


