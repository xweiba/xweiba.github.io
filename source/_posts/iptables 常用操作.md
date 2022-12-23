---
title: iptables 常用操作
date: 2019-07-26 13:29:10
tags:
 - iptables
 - 常用命令
categories:
 - iptables 
---

##  添加端口
```
iptables -A INPUT -p tcp --dport 9115 -j ACCEPT
iptables -A OUTPUT -p tcp --sport 9115 -j ACCEPT
service iptables save
```

## 添加到指定位置
```
# 查看所有行
iptables -L -n --line-number 
# 插入到第三行
iptables -I INPUT 4 -p tcp -m tcp --dport 9115 -j ACCEPT 
iptables -I OUTPUT 1 -p tcp --sport 9115 -j ACCEPT
service iptables save
```


## 删除规则
```
iptables -D INPUT -p tcp --dport 9115 -j ACCEPT
iptables -D OUTPUT -p tcp --sport 9115 -j ACCEPT
service iptables save

```