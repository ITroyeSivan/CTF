---
title: SSTI比较离谱的绕过
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720210308190505.jpg'
authorAbout: SteamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2021-03-29 19:14:58
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1139581.jpg
---
MAR DASCTF放出来的第一道就是个SSTI。过滤很多，试了几个就没耐心做下去了。记得y1ng师傅之前写过一道这种绕过的WP，但是cnblog挂了，所以干脆就等结束了再来学习一下吧。

首先 必须要准备一个环境 这里我推荐vulhub github开源 里面集成了很多框架的漏洞。
搭建完毕：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210329194932.png)

题目直接给了blacklist：
     

    Hi young boy!</br>
    Do you like ssti?</br>
    blacklist</br>   
    '.','[','\'','"',''\\','+',':','_',</br>   
    'chr','pop','class','base','mro','init','globals','get',</br>   
    'eval','exec','os','popen','open','read',</br>   
    'select','url_for','get_flashed_messages','config','request',</br>   
    'count','length','０','１','２','３','４','５','６','７','８','９','0','1','2','3','4','5','6','7','8','9'</br>    
    </br>


比赛的时候就试了常见的几个[]、16进制、request等一些常见的（没看到有源码）

一、UNICODE魔改字符
https://www.compart.com/en/unicode/
比如我们搜索{符号，就会显示几个相似的：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210329211813.png)

网上找的师傅的图：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210329213057.png)

但是我在本地测试并没有成功：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210329213306.png)

可能此方法只在特定环境下有用，据说这道题是可以用的。

二、join拼接
构造globals：

    {% set gl=dict(glo=a,bals=a)|join%}{{gl}}

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210329220916.png)

接下来就是想办法构造数字和特殊字符。（数字可用Unicode上面写了，就不赘述了
一般获取这样的东西的思路是2个，要么利用config，要么利用()|select|string。当然了，一般会想着先弄到数字。数字的思路大概也是2个，要么利用内置的过滤器count或者length，要么用index。这题过滤了count和length，我考虑用index来得到数字（当然，用unicode的话就变得非常简单了，不过我只讲一下不利用unicode的思路），原理是这样：
    {{lipsum}}

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210329221727.png)

    {{lipsum|string|lust}}

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210329222014.png)

    {{(lipsum|string|list).index('f')}}

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210329222347.png)

注：index取得字符第一次出现的位置
但是单引号此题被过滤了，所以用拼接赋值来代替单引号的作用：

    {% set num=dict(f=a)|join%}{{(lipsum|string|list).index(num)}}

成功取到1

lipsum中已有下划线，所以我们还需要方括号和点。

    方括号：{{(lipsum|string|list).pop(18)}}
    点：attr代替

接下来就很简单了，拼接调用完事
直接放别的师傅的payload：
    
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210329225928.png)

构造出来了就可以随意组了，这位师傅的payload确实是有些复杂了，有些地方可以优化的，应该是比赛的时候没时间。
flag就是文件名。
总结：自己还是太懒了。。明明稍微研究一下就能出的东西，可能这就是和大师傅们的差距吧。

链接：
SSTI过滤总结：https://blog.csdn.net/miuzzx/article/details/110220425
WP参考：https://blog.csdn.net/rfrder/article/details/115272645
https://jan.show/?p=59