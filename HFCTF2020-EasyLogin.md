---
title: '[HFCTF2020]EasyLogin'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-08-17 21:47:25
authorLink:
tags: jwt伪造
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1095888.png
---
f12查看源码找到/static/js/app.js

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/hbedrhe.jpg)

后面根据大佬所述得知靠经验读取controllers/api.js

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200817220556.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200817220613.png)

这里有注册、登陆、flag、登出四个路由，可以得知admin登陆后即可获得flag

最开始在抓包的时候其实就已经知道这个思路了。因为注册的时候用admin注册会提示错误，并且加上bp中这几串很明显的字符串就知道是jwt伪造，之前已经遇到过一次了，所以这次虽然没有看源码也能猜到思路。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200817221327.png)

形式：eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.（这里有一个点）eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.（这里也有一个点）TJVA95OrM7E2cBab30RMHrHDcEfxjoYZgeFONFh7HgQ
第一个点前为header，第二个点前为payload，第二个点后为signture
一个攻击点：当header中的alg为none时，后端将不执行签名验证。将alg更改为none后，从JWT中删除签名数据（仅标题+’.'+ payload +’.'）并将其提交给服务器。

此题的利用方式是：将secret置空。利用node的jsonwentoken库已知缺陷：当jwt的secret为空时，jsonwebtoken会采用algorithm为none进行解密。

所以我们得构造一个jwt，将algorithm设为空，将uername设为admin，那么secretid怎么设置才能使secret取出来为空呢？

这里我们还需绕过secretid的一个验证，不能为undefined，不能为null。JavaScript 是一门弱类型语言，可以通过空数组与数字比较永远为真或是小数来绕过，这里用了小数

安装jwt pip install pyjwt

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200817231838.png)

把生成的值替换authorization的值就通过验证了

然后再抓包/api/flag，改一下cookie即可。





