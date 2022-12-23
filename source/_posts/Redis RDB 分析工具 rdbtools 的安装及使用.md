---
title: Redis RDB 分析工具 rdbtools 的安装及使用
date: 2020-08-21 13:17:02
tags:
 - Redis
 - rdbtools
 - RDB
categories:
 - Redis
---

# 安装 rdbtools

该工具需要`python`支持, `2.7` 或 `3.4+` 都可以.

```python
pip install rdbtools
```

# 安装相关依赖

在解析`redis`的`rbd`文件时, 需要安装 `python-lzf`, 否则解析会非常慢.

在`Windows`环境下, 使用`pip`安装`python-lzf`时, 需要安装 `VC++` 依赖对源码进行编译, 而这个依赖极其难装... 

> ps: `python 2.7` 需要`VC++ 9`, `python 3` 需要`VC++ 14`.

为避免浪费时间, 直接下载编译好的 `whl` 文件.

`whl`下载站: [Python Extension Packages for Windows - Christoph Gohlke](https://www.lfd.uci.edu/~gohlke/pythonlibs/#lxml), 打开后浏览器搜索 `python-lzf`, 定位到下载位置. 

根据你的系统版本和`python`版本下载对应的`whl`文件. `pytho3.7` `64`位`Windows`下载 `python_lzf‑0.2.4‑cp37‑cp37m‑win_amd64.whl` .

下载完成后直接安装: `pip install python_lzf‑0.2.4‑cp37‑cp37m‑win_amd64.whl`

# 下载`rdb`

打开`redis`的配置文件, 搜索`dbfilename`, 查看`rdb`文件位置.

如没有自动生成的话, 通过`redis-cli`登陆到`redis`终端执行`bgsave`, 会在后台异步保存当前数据库的数据到`rdb`文件.

下载`rdb`文件至本地`dump.rdb`.

# 生成`rdb`的内存报告

将`rdb`内容解析到`csv`表格中: `rdb -c memory dump.rdb -b 10240 -f dump_memory_v2.csv`.

参数含义:  
`-c`: 该`rdb`保存的数据格式, `memory` 标识内存镜像, 还有`json`等等..  
`-b`: 使用多大的内存来缓冲解析数据, 分片的解析`rdb`数据, 防止内存占用过高.  
`-f`: 解析数据存放的文件位置

生成完毕后可通过表格的排序找出内存占用比较大的`key`.

# 解析单个 `key` 值

解析单个`key`的`value`到文件:`rdb --command json dump.rdb -k "dataenable:router:push" -b 1024 -f dump.json`

想命令行直接输出不要后面的 `-f dump.json` 就行了.

当前这个`key`会生成9G左右的文件, 没一个编辑器打的开...

这种情况咱们可以在运行命令后一两秒后 `ctrl + c` 就结束命令, 生成一个10M左右的文件. 

我们只需要看看数据结构就行了. 