---
title: Linux 常用命令
date: 2021-02-25 13:09:32
tags:
 - Linux
 - 常用命令
categories:
 - Linux
---

# 内存
查看内存
```shell
free -m
```
回收内存
```
# 将所有未写的系统缓冲区写到磁盘中，包含已修改的 i-node、已延迟的块 I/O 和读写映射文件
sync 
# 清空缓存的内存
echo 3 > /proc/sys/vm/drop_caches
```

# 权限
添加用户
```
adduser xxx
```

给用户 `root` 权限
```
vim /etc/passwd
#  将用户id改为 0
tommy:x:0:33:tommy:/data/webroot:/bin/bash
```

# 磁盘
查看磁盘使用率
```
df -h
```

查找大文件
```
# 大于100M的文件 + 表示大于 - 表示小于
find / -type f -size +100M

# 查看当前目录大小
du -sh .

# 查看所有目录大小
du -h --max-depth=1

# 显示前10个占用空间最大的文件或目录
du -s * | sort -nr | head   

# 查找占空间最大的文件与目录
du –max-depth=1 
```

ls 命令
- 按易读方式来显示大小 `ls -lh`

# 文件相关
1. 查找大于 `100M` 的文件
    - find . -type f -size +100M  -print0 | xargs -0 du -h | sort -nr
2. 查找大于 `100M` 的文件并清空
    - find . -type f -size +100M | xargs -I {} sh -c '> {}'
2. 查找大于 `100M` 的文件并删除
    - find . -type f -size +100M | xargs rm -rf
2. 快速查找文件
    - updatedb 更新文件索引数据库
    - locate [fileName]
3. 文件操作
    - 复制时复制软连接 : `cp -d libpng15.so* /usr/local/opencv/4.1.0/libMark`


# 压缩/解压
## tar
1. 压缩 `tar -zcvf` [打包后生成的文件名全路径] [要打包的目录]
    - -c : create 建立压缩档案的参数；
    - -z ： 是否需要用gzip压缩；
    - -v : 压缩的过程中显示档案；
    - -f : 置顶文档名，在f后面立即接文件名，不能再加参数
2. 解压 `tar -zxvf [/source/kernel.tgz] -C [/source/ linux-2.6.29]`
    - -x : 解压缩压缩档案的参数；
    - -C : 指定目录

# 系统相关
1. 查看发行版本 : `cat /etc/redhat-release`
2. 查看内核版本 : `uname -a`
2. 查看 `glibc` 库最高支持版本 : `strings /lib64/libc.so.6 |grep GLIBC_` 
    > glibc是gnu发布的libc库，即c运行库，glibc是linux系统中最底层的api，几乎其它任何运行库都会依赖于glibc。glibc除了封装linux操作系统所提供的系统服务外，它本身也提供了许多其它一些必要功能服务的实现。 
    > 很多linux的基本命令，比如cp, rm, ll,ln等，都得依赖于它，如果操作错误或者升级失败会导致系统命令不能使用，严重的造成系统退出后无法重新进入，所以操作时候需要慎重。
3. `CPU` 信息
    - 查看 `CPU` 型号 : `cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq -c`
    - 查看物理 `CPU` 个数 : `cat /proc/cpuinfo| grep "physical id"| sort| uniq| wc -l`
    - 查看每个物理 `CPU` 中 `core` 的个数(即核数) : `cat /proc/cpuinfo| grep "cpu cores"| uniq`
    - 查看逻辑 `CPU` 的个数 : `cat /proc/cpuinfo| grep "processor"| wc -l`
4. 内存信息
    - 查看内存信息 : `cat /proc/meminfo`

# YUM 包管理器
1. yum erase libselinux-2.0.94-5.3.el6_4.1.x86_64 卸载冲突包



# 文本处理

- 过滤出`openCv` 的 `java`依赖`so`: `libopencv_java410.so` 文件的所有依赖 `so` 目录 `ldd libopencv_java410.so | awk -F '=>' '{print $2}' | awk -F ' ' '{print $1}' | awk '/./ {print}' | grep -v "(" | sort -u`