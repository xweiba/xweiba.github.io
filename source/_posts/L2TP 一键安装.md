---
title: L2TP 一键安装
date: 2019-07-26 13:32:05
tags:
 - L2TP
categories:
 - L2TP
---

使用他的一键脚本 https://teddysun.com/448.html/comment-page-6#comments

如果你要想对用户进行操作，可以使用如下命令：
```
l2tp -a 新增用户
l2tp -d 删除用户
l2tp -m 修改现有的用户的密码
l2tp -l 列出所有用户名和密码
l2tp -h 列出帮助信息
```

其他事项：
```
1、脚本在安装完成后，已自动启动进程，并加入了开机自启动。
2、脚本会改写 iptables 或 firewalld 的规则。
3、脚本安装时，会即时将安装日志写到 /root/l2tp.log 文件里，如果你安装失败，可以通过此文件来寻找错误信息。
```

使用命令：
```
ipsec status （查看 IPSec 运行状态）
ipsec verify （查看 IPSec 检查结果）
/etc/init.d/ipsec start|stop|restart|status （CentOS6 下使用）
/etc/init.d/xl2tpd start|stop|restart （CentOS6 下使用）
systemctl start|stop|restart|status ipsec （CentOS7 下使用）
systemctl start|stop|restart xl2tpd （CentOS7 下使用）
service ipsec start|stop|restart|status （Debian/Ubuntu 下使用）
service xl2tpd start|stop|restart （Debian/Ubuntu 下使用）
```

L2TP debug 模式: 
```
先关闭 L2TP : systemctl stop xl2tpd
已Debug模式启动 : /usr/sbin/xl2tpd -D
```


一键脚本
```sh
wget --no-check-certificate https://raw.githubusercontent.com/teddysun/across/master/l2tp.sh
chmod +x l2tp.sh
./l2tp.sh
```

`options.xl2tpd` 配置

```
[root@host xl2tpd]# cat /etc/ppp/options.xl2tpd 
ipcp-accept-local
ipcp-accept-remote
require-mschap-v2
ms-dns 8.8.8.8
ms-dns 8.8.4.4
noccp
auth
hide-password
idle 1800
mtu 1410
mru 1410
nodefaultroute
debug
proxyarp
connect-delay 5000

#防止两分钟掉线
lcp-echo-interval 0
lcp-echo-failure 0

[root@host xl2tpd]# 
```

`/etc/ipsec.conf` 配置

```
[root@host xl2tpd]# cat /etc/ipsec.conf
version 2.0

config setup
    protostack=netkey
    nhelpers=0
    uniqueids=no
    interfaces=%defaultroute
    virtual_private=%v4:10.0.0.0/8,%v4:192.168.0.0/16,%v4:172.16.0.0/12,%v4:!192.168.18.0/24

conn l2tp-psk
    rightsubnet=vhost:%priv
    also=l2tp-psk-nonat

conn l2tp-psk-nonat
    authby=secret
    pfs=no
    auto=add
    keyingtries=3
    rekey=no
    ikelifetime=8h
    keylife=1h
    type=transport
    left=%defaultroute
    leftid=93.179.100.194
    leftnexthop=%defaultroute
    leftprotoport=17/1701
    right=%any
    rightprotoport=17/%any
    dpddelay=40
    dpdtimeout=130
    dpdaction=clear
    sha2-truncbug=yes
    rightnexthop=%defaultroute
    forceencaps=yes
[root@host xl2tpd]# 
```