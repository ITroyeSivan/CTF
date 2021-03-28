---
title: '[MRCTF2020]Ezpop_Revenge'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/41414542.jpg'
authorAbout: steamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2021-02-02 14:10:03
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1127438.jpg
---
看题目可知pop链，这种题目一般是逻辑漏洞，所以必定是会给源码，直接www.zip拿到源码。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210202142007.png)

flag.php

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210202142034.png)

remote_addr 无法伪造ip 只能找ssrf的点 访问成功后flag写入session。

于是全局查找session_start

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210202145027.png)

Plugin.php中的核心代码如下：

    <?php
    class HelloWorld_DB{
        private $flag="MRCTF{this_is_a_fake_flag}";
        private $coincidence;
        function  __wakeup(){
            $db = new Typecho_Db($this->coincidence['hello'], $this->coincidence['world']);
        }
    }
    class HelloWorld_Plugin implements Typecho_Plugin_Interface
    {
	    public function action(){
            if(!isset($_SESSION)) session_start();
            if(isset($_REQUEST['admin'])) var_dump($_SESSION);
            if (isset($_POST['C0incid3nc3'])) {
		    	if(preg_match("/file|assert|eval|[`\'~^?<>$%]+/i",base64_decode($_POST['C0incid3nc3'])) === 0)
				unserialize(base64_decode($_POST['C0incid3nc3']));
		    	else {
			    	echo "Not that easy.";
			    }
            }
        }
    }

可以看到当传入admin时，输出session，而flag就在session中。同时还存在C0incid3nc3反序列化的点。

上面有个wakeup魔法函数，实例化了Typecho_Db函数，跟进。

没找到Typecho_Db.php，仔细看了下居然是/var/IXR/Typecho/Db.php。。。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210202162124.png)

又是一长串代码，幸好出题人给了提示：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210202225120.png)

全局搜索tostring函数，在/var/IXR/Typecho/Db/Query.php中有__tostring方法

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210203204452.png)

假设$this->_sqlPreBuild['action']为SELECT，在__toString()方法内就会返回$this->_adapter->parseSelect($this->_sqlPreBuild)，调用了$this->_adapter的parseSelect()方法
我们发现这个值我们也是可控的，这个时候我们控制_adapter为soap类就可以了

POP链：首先时/usr下的Plugins.php反序列化调用HelloWorld_DB触发Typecho_Db类，并且可以控制其中的$adapterName
$adapterName拼接到字符串中，触发__tostring，所以这个时候我们使得$adapterName为Query.php中的Typecho_Db_Query类，并且控制私有变量$_adapter为soap类来本地访问flag.php

后面exp抄的y1ng师傅的，暂时没完全理解，就不写了。

https://blog.csdn.net/qq_45691294/article/details/109129120

https://blog.csdn.net/a3320315/article/details/105215741/