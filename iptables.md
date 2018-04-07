### 仅放行
```
iptables -X
iptables -F
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT DROP

iptables -A INPUT -p tcp --dport 22 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 22 -j ACCEPT
iptables -A INPUT -p tcp --dport 21 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 21 -j ACCEPT
iptables -A INPUT -p tcp --dport 6200 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 6200 -j ACCEPT
```

### 仅禁止某端口
```
iptables -A INPUT -p tcp --dport 5900 -j DROP
iptables -A OUTPUT -p tcp --sport 5900 -j DROP
iptables -A INPUT -p tcp --dport 21 -j DROP
iptables -A OUTPUT -p tcp --sport 21 -j DROP
iptables -A INPUT -p tcp --dport 1524 -j DROP
iptables -A OUTPUT -p tcp --sport 1524 -j DROP
iptables -A INPUT -p tcp --dport 513 -j DROP
iptables -A OUTPUT -p tcp --sport 513 -j DROP
iptables -A INPUT -p tcp --dport 514 -j DROP
iptables -A OUTPUT -p tcp --sport 514 -j DROP
iptables -A INPUT -p tcp --dport 23 -j DROP
iptables -A OUTPUT -p tcp --sport 23 -j DROP
iptables -A INPUT -p tcp --dport 512 -j DROP
iptables -A OUTPUT -p tcp --sport 512 -j DROP
iptables -A INPUT -p tcp --dport 25 -j DROP
iptables -A OUTPUT -p tcp --sport 25 -j DROP
iptables -A INPUT -p tcp --dport 111 -j DROP
iptables -A OUTPUT -p tcp --sport 111 -j DROP
iptables -A INPUT -p tcp --dport 2049 -j DROP
iptables -A OUTPUT -p tcp --sport 2049 -j DROP
iptables -A INPUT -p tcp --dport 6667 -j DROP
iptables -A OUTPUT -p tcp --sport 6667 -j DROP
iptables -A INPUT -p tcp --dport 6697 -j DROP
iptables -A OUTPUT -p tcp --sport 6697 -j DROP
iptables -A INPUT -p tcp --dport 1099 -j DROP
iptables -A OUTPUT -p tcp --sport 1099 -j DROP
iptables -A INPUT -p tcp --dport 8787 -j DROP
iptables -A OUTPUT -p tcp --sport 8787 -j DROP
```
