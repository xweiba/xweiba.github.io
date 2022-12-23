---
title: Anacoda 修改 pip 源
date: 2019-11-29 14:08:16
tags:
 - Python
 - Anacoda 
 - Pip
 - 配置
categories:
 - Python
---

> 来源: [更改pip源/anaconda源：windows与linux](https://blog.csdn.net/u012436149/article/details/66974668)

# Windows
在 `c:\user\xxxName\pip\pip.ini` 中加入
```
[global]
# 清华源
index-url=https://pypi.tuna.tsinghua.edu.cn/simple 
[install]  
trusted-host=pypi.tuna.tsinghua.edu.cn
disable-pip-version-check = true  
timeout = 6000  
```
需要 `创建pip文件夹 与 pip.ini 文件`。

# Linux 
```
cd $HOME  
mkdir .pip  
cd .pip
sudo vim pip.conf  

在里面添加  
[global]  
index-url=https://pypi.tuna.tsinghua.edu.cn/simple
[install]  
trusted-host=pypi.tuna.tsinghua.edu.cn 
disable-pip-version-check = true  
timeout = 6000 
```