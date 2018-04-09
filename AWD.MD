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
