---
title: Java Web安全
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720210308190505.jpg'
authorAbout: SteamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2021-04-23 14:16:30
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1142868.png
---
挖不出xx大学的洞，干脆先学点别的。
摘自知识盒子。
## Java基础
Java平台共分为三个主要版本Java SE（Java Platform, Standard Edition，Java平台标准版）、Java EE（Java Platform Enterprise Edition，Java平台企业版）、和Java ME（Java Platform, Micro Edition，Java平台微型版）。

## ClassLoader(类加载机制)
一、ClassLoader(类加载机制)
Java是一个依赖于JVM(Java虚拟机)实现的跨平台的开发语言。Java程序在运行前需要先编译成class文件，Java类初始化的时候会调用java.lang.ClassLoader加载类字节码，ClassLoader会调用JVM的native方法(defineClass0/1/2)来定义一个java.lang.Class实例。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210423214217.png)

二、ClassLoader类加载流程
1、ClassLoader会调用public Class<?> loadClass(String name)方法加载com.anbai.sec.classloader.TestHelloWorld类。
2、调用findLoadedClass方法检查TestHelloWorld类是否已经初始化，如果JVM已初始化过该类则直接返回类对象。
3、如果创建当前ClassLoader时传入了父类加载器(new ClassLoader(父类加载器))就使用父类加载器加载TestHelloWorld类，否则使用JVM的Bootstrap ClassLoader加载。
4、如果上一步无法加载TestHelloWorld类，那么调用自身的findClass方法尝试加载TestHelloWorld类。
5、如果当前的ClassLoader没有重写了findClass方法，那么直接返回类加载失败异常。如果当前类重写了findClass方法并通过传入的com.anbai.sec.classloader.TestHelloWorld类名找到了对应的类字节码，那么应该调用defineClass方法去JVM中注册该类。
6、如果调用loadClass的时候传入的resolve参数为true，那么还需要调用resolveClass方法链接类,默认为false。
7、返回一个被JVM加载后的java.lang.Class类对象。

三、ClassLoader总结
ClassLoader是JVM中一个非常重要的组成部分，ClassLoader可以为我们加载任意的java类，通过自定义ClassLoader更能够实现自定义类加载行为，在后面的几个章节我们也将讲解ClassLoader的实际利用场景。

## Java反射机制
一、Java反射机制
Java反射(Reflection)是Java非常重要的动态特性，通过使用反射我们不仅可以获取到任何类的成员方法(Methods)、成员变量(Fields)、构造方法(Constructors)等信息，还可以动态创建Java类实例、调用任意的类方法、修改任意的类成员变量值等。Java反射机制是Java语言的动态性的重要体现，也是Java的各种框架底层实现的灵魂。

二、获取Class对象
Java反射操作的是java.lang.Class对象，所以我们需要先想办法获取到Class对象，通常我们有如下几种方式获取一个类的Class对象：

1、类名.class，如:com.anbai.sec.classloader.TestHelloWorld.class。
2、Class.forName("com.anbai.sec.classloader.TestHelloWorld")。
3、classLoader.loadClass("com.anbai.sec.classloader.TestHelloWorld");

获取数组类型的Class对象需要特殊注意,需要使用Java类型的描述符方式，如下：

    Class<?> doubleArray = Class.forName("[D");//相当于double[].class
    Class<?> cStringArray = Class.forName("[[Ljava.lang.String;");// 相当于String[][].class

获取Runtime类Class对象代码片段：

    String className     = "java.lang.Runtime";
    Class  runtimeClass1 = Class.forName(className);
    Class  runtimeClass2 = java.lang.Runtime.class;
    Class  runtimeClass3 = ClassLoader.getSystemClassLoader().loadClass(className);

通过以上任意一种方式就可以获取java.lang.Runtime类的Class对象了，反射调用内部类的时候需要使用$来代替.,如com.anbai.Test类有一个叫做Hello的内部类，那么调用的时候就应该将类名写成：com.anbai.Test$Hello。

三、Java反射机制总结
Java反射机制是Java动态性中最为重要的体现，利用反射机制我们可以轻松的实现Java类的动态调用。Java的大部分框架都是采用了反射机制来实现的(如:Spring MVC、ORM框架等)，Java反射在编写漏洞利用代码、代码审计、绕过RASP方法限制等中起到了至关重要的作用。