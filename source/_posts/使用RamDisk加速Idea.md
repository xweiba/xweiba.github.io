---
title: 使用RamDisk加速Idea
date: 2019-08-19 12:23:13
tags:
 - idea
 - Ramdisk
categories:
 - RamDisk
---

E盘为 RamDisk虚拟磁盘。

# 剪切 idea 主要的工作目录
C:\Users\用户名\.IntelliJIdea2019.2 文件夹到 E:\Users\用户名\.IntelliJIdea2019.2 

# mklink 创建文件夹软链接
mklink /J C:\Users\用户名\.IntelliJIdea2019.2 E:\Users\用户名\.IntelliJIdea2019.2 

注意 `C` 盘的 `.IntelliJIdea2019.2` 创建软连接时必须不存在， 所以第一步需要剪切， 或者复制后删除