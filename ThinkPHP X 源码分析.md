---
title: ThinkPHP X 源码分析
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720210308190505.jpg'
authorAbout: SteamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2021-04-02 20:59:07
authorLink:
tags: Real
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-719179.png
---
复现一下ThinkPHP的各种漏洞，锻炼一下自己分析源码的能力，持续更新。

## ThinkPHP 5.0.x (<=5.0.23) RCE分析 2021/3/28

首先查看官方log：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210328215733.png)

改进request的method方法，所以我们diffinity直接对比两个版本区别：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210328220634.png)

在5.0.24中对method作了白名单的限制，只允许$this->method为常用的几个方法，否则就将其置为POST方法，因此我们的入口点就可以从Request.php跟进。
(如果按正常思路寻找的话，index.php->start.php->routeCheck->check 也是可以跟到method的)
全局搜索call_user_func，在Request.php中发现在filterValue方法中。

    private function filterValue(&$value, $key, $filters)
        {
            $default = array_pop($filters);//弹出并返回 array 数组的最后一个单元，并将数组 array 的长度减一。
            foreach ($filters as $filter) {
                if (is_callable($filter)) {//是否能调用
                    $value = call_user_func($filter, $value);
                } elseif (is_scalar($value)) {//检测变量是否是一个标量
                    if (false !== strpos($filter, '/')) {//strpos检测字符第一次出现
                        // 正则过滤
                        if (!preg_match($filter, $value)) {
                            // 匹配不成功返回默认值
                            $value = $default;
                            break;
                        }
                    } elseif (!empty($filter)) {
                        // filter函数不存在时, 则使用filter_var进行过滤
                        // filter为非整形值时, 调用filter_id取得过滤id
                        $value = filter_var($value, is_int($filter) ? $filter : filter_id($filter));
                        if (false === $value) {
                            $value = $default;
                            break;
                        }
                    }
                }
            }
            return $this->filterExp($value);
        }

自己尝试理解下，首先default被赋予filters的最后一个值，然后一个个尝试filters里剩下的值是否为能调用的函数，是的话直接调用，不是的话检测value里是否是一个标量，是的话继续判断filter里有无/，有则将default即filters的最后一个值赋给它。否则（这个否则是针对filters里是否有/）判断filter是否为空，不为空则进行源码注释里的判断。
看似莫名其妙，所以我们全局搜一下调用filterValue的方法：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210328223941.png)

input里面调用了它。
无论$data是不是数组最终都会调用filterValue方法，而$filter则会进行过滤器解析，跟进$this->getFilter方法查看解析过程:

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210328225503.png)

说实话这个我没太理解 暂且继续往下看
回到input方法中，array_walk_recursive函数会对第一个数组参数中的每个元素应用第二个参数的函数。在input类方法中，$data中键名作为filterValue(&$value, $key, $filters)中的value,键值作为key,filter作为第三个参数$filters,而当这些传入到filterValue后，call_user_func又是利用filter作为回调的函数，value作为回调函数的参数，因此也就是input方法中的data是回调函数的参数，filter是需要回调的函数。
了解之后我们需要查找input方法在何处被调用，全局搜索一下：
同文件param方法最后调用该方法并作为返回：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210328234006.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210329004503.png)

    $this->param = array_merge($this->param, $this->get(false), $vars, $this->route(false));

作为data传入input，跟进$this->get

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210329000306.png)

如果$this->get为空，直接将其赋值为$_GET,而最后将$this->get作为input方法的第一个参数，因此我们可以听过变量覆盖，直接将$this->get赋值，就此我们控制了回调函数和参数。

    即_method=__construct&filter[]=system&get[]=whoami或者_method=__construct&filter[]=system&route[]=whoami。

重新推一下，我们可以控制Request类所有方法及属性。
在param方法里，$method = $this->method(true);
跟一下method

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210329002144.png)

再跟进server：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210329002632.png)

$name的值是REQUEST_METHOD。server()方法中又跟进了input()方法，第一个参数$this->server可以利用之前__construct()方法进行属性覆盖，因此$this->server可控。
跟进input()方法：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210328223941.png)

$name是REQUEST_METHOD，会进入if ('' != $name) {。这些代码就相当于$data=$data['REQUEST_METHOD']。而$data就是可控的$this->server，因此这里$data也可控。再往下看：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210328225503.png)

相当于$filter=$this->filter，因此过滤器也可控。接下来就进入了filterValue方法，$data是可控的参数，$filter是可控的函数，再进入利用call_user_func即可RCE。最终构造：

    _method=__construct&filter=system&server[REQUEST_METHOD]=dir

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210329004703.png)

成功执行，危害极大。

漏洞原因：method没有进行限制，导致可以任意调用、覆盖类和属性。
参考：https://www.anquanke.com/post/id/222672
https://blog.csdn.net/rfrder/article/details/114298944

## 5.x < 5.1.31 rce

和上面payload差不多，github上都有，今天主要来学习下提权。

————
学习失败 环境炸了。