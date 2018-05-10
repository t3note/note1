```
<?php echo $_GET[1];?>
curl 127.0.0.1/1.php?1=ls

<?php echo $_POST[1];?>
curl 127.0.0.1/1.php -d "1=ls"

<?php echo $_COOKIE[1];?>
curl 127.0.0.1/1.php -b "1=ls"

<?php echo $_REQUEST[1];?>
（默认情况下包含了 $_GET，$_POST 和 $_COOKIE 的数组。）
curl 127.0.0.1/1.php?1=ls
curl 127.0.0.1/1.php -d "1=ls"
curl 127.0.0.1/1.php -b "1=ls"

<?php echo $_SERVER["HTTP_1"];?>
curl 192.168.0.95/1.php -s -H "1:ls"
(数组取值加上HTTP_，大写)

$src = file_get_contents('php://input');
```
