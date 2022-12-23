---
title: Ubuntu 20.x 修改DNS配置
date: 2022-04-23 13:31:07
tags:
 - Ubuntu
 - 配置
categories:
 - Ubuntu
---

> 家里部署了一台 `Ubuntu`, 发现 `raw.githubusercontent.com` 域名 `DNS` 竟然被污染了... 记录下一下, 20 的配置文件和以前不一样

# 1. Ubuntu 18.04 LTS 及以上版本
配置文件变更为：`/etc/netplan/*.yaml`。例如：
```yaml
# This is the network config written by 'subiquity'
network:
  ethernets:
    ens33:
      dhcp4: true
      nameservers:
        addresses:
          - 114.114.114.114
          - 223.5.5.5
  version: 2
```

# 2. Ubuntu 18.04 LTS 以下版本
主要修改两个文件。

## 2.1 更新 resolv.conf
首先修改文件：`/etc/resolvconf/resolv.conf.d/base` 文件，内容如下：
```
nameserver 114.114.114.114
nameserver 223.5.5.5
```

## 2.2 network/interfaces
然后修改文件：`/etc/network/interfaces` 文件，删除其中的 `dns-nameservers` 配置项：
```
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
auto ens33
#iface ens33 inet dhcp
iface ens33 inet static
address 192.168.2.231
netmask 255.255.255.0
gateway 192.168.2.1
#dns-nameservers 202.102.134.68 202.102.128.68
```

然后重启 `network` 服务：`/etc/init.d/networking restart` ，这样就可以了。

实际修改中，大部分服务器修改第一个就可以起效了，只有个别的需要修改两个。为了防止歧义，推荐两个都修改。