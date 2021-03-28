---
title: 'redis安全学习笔记&[网鼎杯 2020 玄武组]SSRFMe'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/41414542.jpg'
authorAbout: steamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2021-01-17 13:13:11
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1123188.jpg
---
前几个月有个比赛有类似的一道题，当时百度了一个payload没来得及去深究，今天正好又遇到了，简单分析一下。

    <?php
    function check_inner_ip($url)
    {
        $match_result=preg_match('/^(http|https|gopher|dict)?:\/\/.*(\/)?.*$/',$url);
        if (!$match_result)
        {
            die('url fomat error');
        }
        try
        {
            $url_parse=parse_url($url);
        }
        catch(Exception $e)
        {
            die('url fomat error');
            return false;
        }
        $hostname=$url_parse['host'];
        $ip=gethostbyname($hostname);
        $int_ip=ip2long($ip);
        return ip2long('127.0.0.0')>>24 == $int_ip>>24 || ip2long('10.0.0.0')>>24 == $int_ip>>24 || ip2long('172.16.0.0')>>20 == $int_ip>>20 || ip2long('192.168.0.0')>>16 == $int_ip>>16;
    }

    function safe_request_url($url)
    {

        if (check_inner_ip($url))
        {
            echo $url.' is inner ip';
        }
        else
        {
            $ch = curl_init();
            curl_setopt($ch, CURLOPT_URL, $url);
            curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
            curl_setopt($ch, CURLOPT_HEADER, 0);
            $output = curl_exec($ch);
            $result_info = curl_getinfo($ch);
            if ($result_info['redirect_url'])
            {
                safe_request_url($result_info    ['redirect_url']);
            }
            curl_close($ch);
            var_dump($output);
        }

    }
    if(isset($_GET['url'])){
        $url = $_GET['url'];
        if(!empty($url)){
            safe_request_url($url);
        }
    }
    else{
        highlight_file(__FILE__);
    }
    // Please visit hint.php locally.
    ?>

两个函数，check_inner_ip($url)检测你是不是内网ip，safe_request_url($url)在判断你不是内网ip之后就可以执行一系列命令。而题目提示必须本地访问hint.php。这样看上去似乎没有漏洞，无法读取hint.php，但这里有一个注意点：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210117135921.png)

curl 和 php_url_parse 处理后最终的目标不一样，curl认为evil.com:80是目标而parse_url认为google.com是目标.
作者向 curl 团队报告了这个问题，得到了一个补丁，但是补丁又可以通过空格的方式绕过。

预期：url=http://caiji@127.0.0.1:80 @baidu.com/hint.php
但是这预期我打不出来不知道啥原因，所以就用了非预期
非预期：url=http://0.0.0.0/hint.php  //0.0.0.0 代表本机ipv4 的所有地址

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210117141325.png)

后面是一个redis主从复制，会用就行。

https://blog.csdn.net/qq_43756333/article/details/107536521

接下来了解一下redis。

Redis 是一个高性能的key-value数据库。 redis的出现，很大程度补偿了memcached这类key/value存储的不足，在部 分场合可以对关系数据库起到很好的补充作用。它提供了Java，C/C++，C#，PHP，JavaScript，Perl，Object-C，Python，Ruby，Erlang等客户端，使用很方便。
 
Redis支持主从同步。数据可以从主服务器向任意数量的从服务器上同步，从服务器可以是关联其他从服务器的主服务器。这使得Redis可执行单层树复制。存盘可以有意无意的对数据进行写操作。由于完全实现了发布/订阅机制，使得从数据库在任何地方同步树时，可订阅一个频道并接收主服务器完整的消息发布记录。同步对读取操作的可扩展性和数据冗余很有帮助。

浅析SSRF认证攻击Redis：https://www.smi1e.top/%E6%B5%85%E6%9E%90ssrf%E8%AE%A4%E8%AF%81%E6%94%BB%E5%87%BBredis/
