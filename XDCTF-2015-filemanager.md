---
title: '[XDCTF 2015]filemanager'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-11-27 20:05:37
authorLink:
tags: 二次注入
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1115558.jpg
---

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201127201044.png)

www.tar.gz泄露源码

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201128224459.png)

还是比较明显的二次注入，因为最近做题的时候看源码都看得更细了些，所以这题写了两天（其实是昨天划水了，《你的名字。》太好哭了呜呜呜

简单总结一下此题原理，即利用二次注入，将extension后缀名置为空，由下图可知，白名单只会判断后缀名而不会判断文件名，所以我们就可以直接上传1.jpg.jpg即可。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201128230243.png)

解题：

1、首先上传一个文件名为：',extension='',filename='troye.jpg.jpg的文件，此时数据库中文件名为troye.jpg。
至于为什么会执行，看源码可知在初次上传时addslashes函数对文件名进行了转义，所以单引号也被传进去了，而在rename时，调用oldname的时候由于单引号的存在就被代入执行了。
这就不细说了，二次注入遇到好几次了。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201128231425.png)

2、重命名

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201128231624.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201128231526.png)

3、上传一个真正带webshell的troye.jpg，然后再次重命名

注意在这里有个坑，这里改名的时候检查了文件是否存在：if(file_exists($oldname)) 虽然通过注入修改了filename的值，但我upload目录下上传的文件名是没有改的。 因为利用注入时将extension改为空了，那么实际上数据库中的filename总比文件系统中真实的文件名少一个后缀。 那么这里的file_exists就验证不过。这里可以通过再次上传一个新文件，这个文件名就等于数据库里的filename的值就即可绕过。 所以最后整个getshell的流程，实际上是一个二次注入+二次操作getshell。

4、getflag

1=system('cd ../../../../;cat f*');