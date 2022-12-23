---
title: Java 服务中文乱码
date: 2018-07-26 12:58:33
tags:
 - Java
 - 乱码
categories:
 - Java
---

> 最近发现运维大哥在新环境部署相关服务后生成的数据出现中文乱码的问题, 记录一下

# 修改系统编码环境变量
`locale` 查看当前系统编码:

![](https://ws1.sinaimg.cn/large/b08fb805gy1g54w32mrscj20ag06i0so.jpg)

这是修改后正常状态, 如不是需修改 `cat /etc/sysconfig/i18n` 文件, 修改为:
```
LANG="zh_CN.UTF-8"
```
即可, 这是 `centOS6.*`, 其他发行版可能是别的文件.

改完后刷新环境变重启服务即可生效
```
source /etc/sysconfig/i18n
```