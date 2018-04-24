### 目录备份及其改动监控
```
while true;do d=`diff -r ./html /var/www/html`;l=${#d};if (($l != "0"));then echo $d;rm -fr /var/www/html;cp -fr ./html /var/www;fi;done
```
```
scp -r -P 2221 hack@172.16.2.234:/var/www/html /tmp/mmm
cp /tmp/mmm/html  /tmp/mmm/html2
加固修改后：
scp -r -P 2221 /tmp/mmm/html hack@172.16.2.234:/var/www
```

```
rm -fr *
ls -al
rm -fr .*
scp -r -P 2221 hack@172.16.2.234:/var/www/html ./
cd html
mkdir ../html_bak
curl --help|grep time
ls /tmp/mmm1/html_no/
//目录结构
cp -fr ../html /tmp/mmm1/html_no/
for i in `grep php -lr`;do echo $i;curl 172.16.2.234:9090/$i -s --max-time 3 > /tmp/mmm1/html_bak/html/$i;done
scp -P 2211 -r /tmp/mmm1/html_no/html hack@172.16.2.234:/var/www
```

我们执行的chmod命令，其底层实现是chattr命令，用此命的功能更为强大，甚至可以锁定文件，即使root用户也操作不了此文件。


Linux下批量替换多个文件中的字符串的简单方法。用sed命令可以批量替换多个文件中的字符串。

命令如下:
```
sed -i "s/原字符串/新字符串/g" `grep 原字符串 -rl /var/www/html`

sed -i "s/eval(/intval(/g" `grep eval -rl /var/www/html`
sed -i "s/intval(/@intval(/g" `grep val -rl ./`
sed -i "s/<?php/<?php\n\n\n/g" `grep php -rl ./`
//美元符号用右斜杠转义
sed -i "s/\$p/1/g" `grep \$ -rl ./`
```
