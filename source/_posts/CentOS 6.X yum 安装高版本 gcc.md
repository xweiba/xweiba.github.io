---
title: CentOS 6.X yum 安装高版本 gcc
date: 2021-01-07 13:10:28
tags:
 - Linux
 - CentOs
 - GCC
 - yum
categories:
 - Linux
---

> [为CentOS 6、7升级gcc至4.8、4.9、5.2、6.3、7.3等高版本](https://www.vpser.net/manage/centos-6-upgrade-gcc.html)

# 添加yum源安装GCC 

## 安装
### GCC 4.8 安装
```
wget http://people.centos.org/tru/devtools-2/devtools-2.repo -O /etc/yum.repos.d/devtools-2.repo
 
yum install devtoolset-2-gcc devtoolset-2-binutils devtoolset-2-gcc-c++
```

### GCC 4.9 安装
```
wget https://copr.fedoraproject.org/coprs/rhscl/devtoolset-3/repo/epel-6/rhscl-devtoolset-3-epel-6.repo -O /etc/yum.repos.d/devtools-3.repo
yum install devtoolset-3-gcc devtoolset-3-binutils devtoolset-3-gcc-c++
```

### GCC 5.2 安装
```
wget https://copr.fedoraproject.org/coprs/hhorak/devtoolset-4-rebuild-bootstrap/repo/epel-6/hhorak-devtoolset-4-rebuild-bootstrap-epel-6.repo -O /etc/yum.repos.d/devtools-4.repo
yum install devtoolset-4-gcc devtoolset-4-binutils devtoolset-4-gcc-c++
```

## 将系统GCC切换到安装的版本
切换版本:
```
scl enable devtoolset-2 bash # 4.8
scl enable devtoolset-3 bash # 4.9
scl enable devtoolset-4 bash # 5.2
```
需要注意的是scl命令启用只是临时的，退出shell或重启就会恢复原系统gcc版本。
如果要长期使用的话：
```
echo "source /opt/rh/devtoolset-版本号/enable" >>/etc/profile
```