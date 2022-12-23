---
title: linux 创建 swap 交换分区
date: 2019-11-07 13:21:12
tags:
 - Linux
 - Swap
categories:
 - Linux
---

> 大部分云主机都是没有开启 swap 交换分区的, 且内存都在 1-2G 左右, 在编译源码时可能会因为内存不足而编译失败.

# 1. 创建一个空文件
- sudo dd if=/dev/zero of=/swapfile bs=1M count=2048
# 2. Bake swap file:
- sudo mkswap /swapfile
# 3. 开机时启动:
```
# 把下面这行加到 /etc/fstab
/media/fasthdd/swapfile.img swap swap sw 0 0
```
# 4. 激活:
- swapon /swapfile
# 5. 验证是否成功
- cat /proc/swaps
    ```linux
    [root@host build]# cat /proc/swaps
    Filename                                Type            Size    Used    Priority
    /swap                                   file            135164  135112  -2
    /var/swapfile                           file            524284  491508  -3
    /swapfile                               file            2097148 31916   -4
    ```
- grep 'Swap' /proc/meminfo
    ```linux
    [root@host build]# grep 'Swap' /proc/meminfo
    SwapCached:        32752 kB
    SwapTotal:       2756596 kB
    SwapFree:        2098060 kB
    ```