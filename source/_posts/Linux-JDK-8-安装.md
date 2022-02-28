---
title: Linux JDK 8 安装
date: 2020-09-28 11:03:07
tags: 
 - Linux
 - JDK8
categories:
 - JDK
---

> demo新环境, 非`Docker`记录一下~

# 下载JDK8

`Oracle` 下载需要注册, 直接去`github`上下载~~ 

项目地址: [oracle-java](https://github.com/frekele/oracle-java/releases)

`JDK8 Linux x64` 下载地址: [jdk-8u212-linux-x64.tar.gz](https://github.com/frekele/oracle-java/releases/download/8u212-b10/jdk-8u212-linux-x64.tar.gz)

上传至服务器解压即可~

# 配置环境变量

在文件 `/etc/profile` 末尾添加 `JAVA` 相关环境变量:

```bash
vim /etc/profile
JAVA_HOME=/home/icampus3.0/jdk_1.8.0_212
JRE_HOME=$JAVA_HOME/jre
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
export JAVA_HOME JRE_HOME PATH CLASSPATH
```

应用环境变量:

```bash
source /etc/profile
```

# 检查一下

命令行执行:

```bash
java -version
```

