我们执行的chmod命令，其底层实现是chattr命令，用此命的功能更为强大，甚至可以锁定文件，即使root用户也操作不了此文件。


Linux下批量替换多个文件中的字符串的简单方法。用sed命令可以批量替换多个文件中的字符串。

命令如下:
```
sed -i "s/原字符串/新字符串/g" `grep 原字符串 -rl /var/www/html`

sed -i "s/eval(/intval(/g" `grep eval -rl /var/www/html`
sed -i "s/intval(/@intval(/g" `grep val -rl ./`
sed -i "s/<?php/<?php\n\n\n/g" `grep php -rl ./`
```
