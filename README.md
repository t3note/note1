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

## vmware转kvm
将vmdk和vmx传到kvm主机：
qemu-img convert '/home/test/桌面/Metasploitable.vmdk' -f vmdk -O qcow2 '/home/test/桌面/Metasploitable.qcow2'

