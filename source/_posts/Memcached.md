---
title: Memcached
date: 2018-08-14 13:35:23
tags:
 - Memcached
categories:
 - Memcached
---

## 安装准备
1. Linux系统安装memcached，首先要先安装libevent库。
- Ubuntu/Debian: `sudo apt-get install libevent libevent-deve`
- Redhat/Fedora/Centos `yum install -y libevent libevent-deve`

## 安装
1. 仓库安装
- Ubuntu/Debian: `sudo apt-get install memcached`
- Redhat/Fedora/Centos: `yum install -y memcached`
2. 源码安装
- 从其官方网站（http://memcached.org）下载memcached最新版本并编译.
```sh
wget http://memcached.org/latest                    下载最新版本 
tar -zxvf memcached-1.x.x.tar.gz                    解压源码
cd memcached-1.x.x                                  进入目录
./configure --prefix=/usr/local/memcached           配置
make && make test                                   编译
sudo make install                                   安装
```

## 运行
1. 仓库安装
- 前台显示运行: `memcached -p 11211 -m 64m -vv -u root`
- 后端守护进程运行: `memcached -p 11211 -m 64m -u root -P /tmp/memcached.pid -d`
2. 源码安装 
- 前端显示运行: `/usr/local/memcached/bin/memcached -p 11211 -m 64m -vv`
- 后端守护进程运行: `/usr/local/memcached/bin/memcached -p 11211 -m 64m -u root -P /tmp/memcached.pid -d`

3. 启动选项详情: 
- `-h` 命令帮助
- `-d`是启动一个守护进程；
- `-m`是分配给Memcache使用的内存数量，单位是MB;
- `-u`是运行Memcache的用户；
- `-l`是监听的服务器IP地址，可以有多个地址；
- `-p`是设置Memcache监听的端口，，最好是1024以上的端口；
- `-c`是最大运行的并发连接数，默认是1024；
- `-P`是设置保存Memcache的pid文件.
- `-vv`是显示详情

## 常用命令
1. 连接
- `telnet HOST PORT`
2. Memcached 存储命令
- set 命令用于将 value(数据值) 存储在指定的 key(键) 中
- > set key flags exptime bytes [noreply] ]
- > value 
```
参数说明: 
- key：键值 key-value 结构中的 key，用于查找缓存值。
- flags：可以包括键值对的整型参数，客户机使用它存储关于键值对的额外信息 。
- exptime：在缓存中保存键值对的时间长度（以秒为单位，0 表示永远）
- bytes：在缓存中存储的字节数
- noreply（可选）： 该参数告知服务器不需要返回数据
- value：存储的值（始终位于第二行）（可直接理解为key-value结构中的value）