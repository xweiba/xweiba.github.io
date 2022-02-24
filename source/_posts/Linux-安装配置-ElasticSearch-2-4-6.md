---
title: Linux 安装配置 ElasticSearch-2.4.6
date: 2020-09-29 16:01:01
tags: 
 - Linux
 - ElasticSearch
---

> ES最新版已经到7.x了,  想用最新版啊, 多了很多特性.但是这个环境是做业务测试的, 原来用的就是 `2.4.6` , 防止出问题, 延续了.

<!--more-->

#  下载安装

进入工作目录, 执行即可:

```bash
wget https://download.elastic.co/elasticsearch/release/org/elasticsearch/distribution/tar/elasticsearch/2.4.6/elasticsearch-2.4.6.tar.gz
tar -zxvf elasticsearch-2.4.6.tar.gz
cd elasticsearch-2.4.6/ 
```

# 基础配置

编辑 `vim config/elasticsearch.yml` 文件, 放开以下配置的注释并修改.

```yml
cluster.name: test-es
node.name: master
network.host: 192.168.134.22 # 
http.port: 8200
discovery.zen.ping.unicast.hosts: ["192.168.100.35"]
```

# 启动和关闭

## 启动

```bash
./bin/elasticsearch -Des.insecure.allow.root=true 
```

## 守护模式

```bash
./bin/elasticsearch -Des.insecure.allow.root=true -d 
```

## 关闭

直接 `kill` 进程

```bash
kill -9 pid
```

## 加个脚本

`vim start.sh `

```shell
#!/bin/bash

echo "elasticsearch start..."
/home/icampus3.0/elasticsearch-2.4.6/bin/elasticsearch -Des.insecure.allow.root=true -d
```

`vim stop.sh`

```shell
#!/bin/bash
#created by code

echo "elasticsearch stoped....."
pid=$(ps aux|grep -v grep|grep "elasticsearch"|awk '{print $2}');
if [[ $pid -gt 1 ]]; then
    kill -9 $pid
fi
```

`vim restart.sh`

```
#!/bin/bash

cd /home/icampus3.0/elasticsearch-2.4.6
./stop.sh
./start.sh
```

给权限 :

`chmod 755 start.sh stop.sh restart.sh`