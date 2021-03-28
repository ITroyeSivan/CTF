---
title: HGAME-Web-Week1WP
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/41414542.jpg'
authorAbout: steamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2021-02-06 20:00:00
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1127455.jpg
---
一、Hitchhiking_in_the_Galaxy

抓包我要搭顺风车

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210204134003.png)

显示405 Method Not Allowed，burp改请求方式为POST

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210204134107.png)

改User-Agent: Infinite Improbability Drive

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210204134222.png)

改Referer: https://cardinal.ink/

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210204134314.png)

改X-Forwarded-For: 127.0.0.1

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210204135007.png)

二、watermelon

project.js修改每次合成增加的分数即可，或者改每次都掉大西瓜，解法很多。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210204134515.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210204135024.png)

三、宝藏走私者

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210204161610.png)

https://paper.seebug.org/1048

四、智商检测鸡

根据session判断solve的数量，由于不知道密钥，所以无法直接伪造，只能写个脚本自动做题。写脚本的时候注意cookie的变化即可。

    #!/usr/bin/python3
    # @Author: Troy3e
    import requests
    import re
    import json
    
    url1 = 'http://r4u.top:5000/api/getQuestion'
    url2 = 'http://r4u.top:5000/api/getStatus'
    url3 = 'http://r4u.top:5000/api/verify'
    url4 = 'http://r4u.top:5000/api/getFlag'
    hea = {
      'User-Agent': 'Mozilla/5.0 (Windows NT 6.3; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2272.118 Safari/537.36'
    }
    hea2 = {
      'User-Agent': 'Mozilla/5.0 (Windows NT 6.3; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2272.118 Safari/537.36',
      'Content-Type': 'application/json'
    }
    a = 1
    while 1:
      source1 = requests.get(url=url2,headers=hea)
      source1.encoding = 'utf-8'
      if a==1:
        cookie = source1.headers['Set-Cookie']
      res = source1.text
      print(res)
      hea = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 6.3; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2272.118 Safari/537.36',
        'Cookie': cookie
      }
      hea2 = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 6.3; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/41.0.2272.118 Safari/537.36',
        'Content-Type': 'application/json',
        'Cookie': cookie
      }
      if '{"solving":99}' in res:
        source4 = requests.get(url=url4,headers=hea)
        print(source4.text)
        break
      source2 = requests.get(url=url1,headers=hea)
      source2.encoding = 'utf-8'
      ques = source2.text
      num = re.findall("\d+",ques)
      a = int(num[1])
      b = int(num[2])
      c = int(num[3])
      d = int(num[4])
      tmpresult = c / 2 * b * b + d * b - c / 2 * a * a + d * a
      result1 = {"answer":tmpresult}
      result = json.dumps(result1)
      print(result)
      source3 = requests.post(url=url3,headers=hea2,data=result)
      print(source3.text)
      cookie = source3.headers['Set-Cookie']
      a=a+1

五、走私者的愤怒

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210204180522.png)