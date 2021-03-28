---
title: '[RoarCTF 2019]Easy Java'
author: Troy3e
avatar: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg
authorLink: 
authorAbout: 一个好奇的人
authorDesc: 一个好奇的人
categories: 技术
comments: true
date: 2020-07-07 10:55:02
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-542249.jpg
---
进去以后如图所示：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200707122044.png)
尝试admin 123456提示密码错误。
打开下面的help出现：java.io.FileNotFoundException:{help.docx}
url：http://f72eefe1-b91c-4b64-af67-9bd43f96bf1c.node3.buuoj.cn/Download?filename=help.docx
查看wp，知道可以尝试将请求方式由GET换为POST，POST后，得到help.docx，如图所示
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200228201203675.png)
再次查看wp，知道这是源码泄露题
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200707123821.png)
根据help里的java推测这里是WEB-INF/web.xml泄露
再次用post方式提交，果然得到源码：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200707124211.png)
虽然看不懂，但是发现了这个flagcontroller
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200707124414.png)
访问/Flag：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200707124835.png)
最后payload：filename=/WEB-INF/classes/com/wm/ctf/FlagController.class
base64解码文件中的代码即可





参考链接：https://blog.csdn.net/wy_97/article/details/78165051
https://blog.csdn.net/silencediors/article/details/102579567?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.edu_weight&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.edu_weight