---
title: Linux Python 2.6 升级至 2.7
date: 2021-01-07 13:12:18
tags:
 - Linux
 - Python
 - 升级
categories:
 - Linux
---

> 来自: [CentOs 6.x 升级 Python 版本 至2.7.X](https://www.jianshu.com/p/decfcec4dbfd)

# 一. 更新系统和开发工具集及所需依赖
## 1. 更新系统及开发工具
```
yum -y update # 可不做
yum groupinstall -y 'development tools'
```

## 2. 安装 python 工具需要的额外软件包 SSL, bz2, zlib
```
yum install -y zlib-devel bzip2-devel openssl-devel xz-libs wget
```

## 3. 下载源码并解压
```
wget https://www.python.org/ftp/python/2.7.9/Python-2.7.9.tar.xz
Or 
镜像地址: wget http://mirrors.sohu.com/python/2.7.9/Python-2.7.9.tar.xz
xz -d Python-2.7.9.tar.xz
tar -xvf Python-2.7.9.tar -C ./
```

# 二. 编译安装
## 1. 编译配置
```
cd Python-2.7.9
./configure --prefix=/usr/local
make 
make altinstall
```

## 2. 检查是否安装成功
```
python2.7 -V
```

`opencv` 编译时是直接调用 `python2.7` 的, 可以不做第三步

# 三. 配置环境变量或使用软链接替换原python命令
## 1. 设置软连接
```
ln -sf /usr/local/bin/python2.7 /usr/bin/python
```

## 2. 配置环境变量, 如过原来安装过应该不用配置
```
vim /etc/profile
export PATH="/usr/local/bin:$PATH"
```

**CentOS 需要修改yum文件第一行, 否则yum会报错:** `vim /usr/bin/yum`
```
#!/usr/bin/python2.6
```