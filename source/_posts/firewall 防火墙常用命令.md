---
title: firewall 防火墙常用命令
date: 2019-07-26 13:29:59
tags:
 - firewall
 - 常用命令
categories:
 - firewall
---

## 安装 `firewalld`
`yum install firewalld firewall-config`

## 你也可以关闭目前还不熟悉的FirewallD防火墙，而使用iptables，命令如下：
```sh
systemctl stop firewalld
systemctl disable firewalld
yum install iptables-services
systemctl start iptables
systemctl enable iptables
```
## 常用命令
-  启动 `systemctl start  firewalld`
-  禁用 `systemctl stop firewalld`
-  开机启动 `systemctl enable firewalld`
-  关闭开机自启 `systemctl disable firewalld`
-  状态 `systemctl status firewalld` | `systemctl status firewalld.service` | `firewall-cmd --state`
- 查看版本 `firewall-cmd --version`
- 查看公开域开放端口 `firewall-cmd --zone=public --list-ports`
- 查看公开域开放的服务 `firewall-cmd --zone=public --list-service`
- 添加端口, 添加后需要重载firewall才会生效, `--perament` 设置为永久生效 
`firewall-cmd --zone=public --add-port=9080/tcp --permanent`
- 删除端口 `firewall-cmd --zone=public --remove-port=9080/tcp --permanent`
- 重新载入 `firewall-cmd --reload`
- 查看指定端口 `firewall-cmd --zone=public --query-port=9080/tcp`
- 查看当前允许的端口及服务 `firewall-cmd --list-all`

## 服务相关
- 查看所有服务 `firewall-cmd --get-service`
- 允许服务 `firewall-cmd --add-service=mysql --permanent`