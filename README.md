# note1
以下命令可以实现在命令行中设置用户属性为密码永不过期（需要安装wmic.exe环境，执行命令时，请将半角双引号中的“用户名”换成实际的用户名，windows7中验证通过）：
```
wmic.exe UserAccount Where Name="用户名" Set PasswordExpires="false"
```

03 xp直接开3389 不用重启：
```
REG ADD HKLM\SYSTEM\CurrentControlSet\Control\Terminal" "Server /v fDenyTSConnections /t REG_DWORD /d 00000000 /f
```

mysql如何开启远程连接

```
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;
```
使用“flush privileges;”命令刷新刚才修改的权限，使其生效。

qcow2压缩办法：
```
qemu-img convert -c -O qcow2 win.qcow2 ocr.qcow2
```
其中ocr.qcow2是你的目标镜像

```
打包压缩
tar -zcvf AWD.tar.gz AWD/
解压
tar -zxvf AWD.tar.gz
```
shell屏蔽错误的输出：
```
echo "test" > /root/file 2>/dev/null
上面的命令执行，会首先>/root/file创建文件，发现没权限便报错直接退出，不会继续其他操作
    （echo "test" > /root/file）2>/dev/null
这个就会把（）里面的所有错误输出导向/dev/null
```
shell屏蔽所有输出：
```
（echo "test" > /root/file）2>/dev/null >/dev/null 
```


