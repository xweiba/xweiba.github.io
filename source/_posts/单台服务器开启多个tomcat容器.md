---
title: 单台服务器开启多个tomcat容器
date: 2018-09-23 13:23:54
tags:
 - Tomcat
 - Shell
categories:
 - Tomcat
---

## 复制一份 tomcat 作为备用容器
1. `cp -R tomcat/ tomcat_2`

## 设置环境变量
1. 编辑环境变量 `vim /etc/profile`, 最后一行添加:
```sh
##########first tomcat###########
CATALINA_BASE=/www/server/tomcat
CATALINA_HOME=/www/server/tomcat
TOMCAT_HOME=/www/server/tomcat
export CATALINA_BASE CATALINA_HOME TOMCAT_HOME
##########second tomcat##########
CATALINA_2_BASE=/www/server/tomcat_2
CATALINA_2_HOME=/www/server/tomcat_2
TOMCAT_2_HOME=/www/server/tomcat_2
export CATALINA_2_BASE CATALINA_2_HOME TOMCAT_2_HOME
```
2. 更新环境变量 `source /etc/profile`

## 配置 tomcat_2
1. 配置启动环境变量, 进入 `tomcat_2` 目录, 编辑其 `bin/catalina.sh`, 找到`OS specific support.  $var _must_ be set to either true or false.`, 插入 `tomcat2` 的环境变量
```sh
# OS specific support.  $var _must_ be set to either true or false.
# 添加tomcat2 环境变量
export CATALINA_BASE=$CATALINA_2_BASE
export CATALINA_HOME=$CATALINA_2_HOME
...
```
2. 配置容器端口, 编辑 `tomcat_2/conf/server.xml`, 分别修改下列几个端口号, 与 `tomcat1` 的端口不同即可.
```xml
<!-- 8005=>9005 tomcat shutdown端口-->
<Server port="9005" shutdown="SHUTDOWN">
```
```xml
<!-- 8080=>9080 tomcat web端口-->
<Connector port="9080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443" />
```
```xml
<!-- 8009=>9009 AJP1.3端口 -->
<Connector port="9009" protocol="AJP/1.3" redirectPort="8443" />
```

3. 防火墙放通端口 `8080` `9080`
- `firewall-cmd --zone=public --add-port=8080/tcp --permanent`
- `firewall-cmd --zone=public --add-port=9080/tcp --permanent`
- `firewall-cmd --reload`

4. 启动容器
- 分别运行对应目录启动脚本 `bin/startup.sh`

5. 通过服务器ip:端口号测试