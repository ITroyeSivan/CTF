---
title: Laravel漏洞分析
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720210308190505.jpg'
authorAbout: SteamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2021-04-17 12:14:05
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1140593.jpg
---
又给自己挖了个大坑。。。

## 通过phpStudy搭建Laravel
从github上把源码down下来：https://github.com/laravel/laravel/releases?after=v5.8.35
然后在根目录打开cmd：composer intsall 生成vender目录
改hosts 路径：C:\Windows\System32\drivers\etc\hosts
添加127.0.0.1 www.fuck.com(Laravel必须由域名访问
最后把根目录下.env.example中的内容复制到.env中（记得localhost改成www.fuck.com），cmd中执行php artisan key:generate即可。
访问出现没有error就是搭建成功：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210417221038.png)
————————
后记：改hosts那一步可能不是必须的。
## 如何利用phpStorm进行动态调试
这一步是后面要用的，在进行代码审计时，通过调试进行分析是非常重要的，所以虽然有些小麻烦，但是必须会用。
需要的环境：phpStorm+Xdebug+phpStudy
phpStudy自带了xdebug插件，但是默认是关闭的，手动选择开启：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210418141556.png)

更改配置文件，我这里用的是7.3.4nts

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210418141751.png)
只要改选中的部分，别的默认即可：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210418141902.png)
（这里我用9100端口是因为有时候9000会被占用，为了避免后面冲突的麻烦换成了和网上教程都不一样的9100）

接下来 浏览器上下载Xdebug helper

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210418142135.png)
在url栏后面的小虫。

然后就是配置phpStorm

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210418143756.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210418143825.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210418143904.png)

下断点 浏览器刷新：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210418144030.png)
红点中打勾就说明已经成功断下来了。
## Laravel 5.7.x 反序列化漏洞分析
laravel 5.7是一款基于php 7.1.3之上运行的优秀php开发框架,反序列化RCE漏洞出现在核心包中，但是需要对基于laravel v5.7框架进行二次开发的cms出现可控反序列化点，才能触发漏洞实现RCE。

由于在本地搭建的仅仅是Laravel一个空架子，所以还需要手动添加一个反序列化点。
在routes\web.php增加一个路由:

    Route::get('/index','TaskController@index');

新建app\Http\Controllers\TaskController.php，并且在此增加反序列化入口：

    <?php
    namespace App\Http\Controllers;
    
    class TaskController
    {
        public function index(){
            if(isset($_GET['ser'])){
                $ser = $_GET['ser'];
                unserialize($ser);
                return ;
            }else{
                echo "no unserialization";
                return ;
            }
        }
    }
    ?>
将Laravel 5.6和5.7版本进行对比，在vendor\laravel\framework\src\Illuminate\Foundation\Testing\下新版本多出了一个文件PendingCommand.php：
public$test;//一个实例化的类 Illuminate\Auth\GenericUser
protected$app;//一个实例化的类 Illuminate\Foundation\Application
protected$command;//要执行的php函数 system
protected$parameters;//要执行的php函数的参数  array('id')

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210417225934.png)
PendingCommand.php中run函数用来执行命令，而destruct中又调用了run函数。所以简单的 POP 链为：构造的 exp 经过反序列化后调用 __destruct()，进而调用 run(),run() 进行代码执行。
严格来说应该是返回执行结果，不知道自己理解的对不对。毕竟是call的。
现在通过exp来动态调试对利用链进行分析:
在执行unserialize后需要加载类，这里是通过src\Illuminate\Foundation\AliasLoader.php中load方法进行类的加载：

桥豆麻袋，在这一步之前需要会用phpStorm进行动态调试，教程在上面。

跟进unserialize：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210418144310.png)
