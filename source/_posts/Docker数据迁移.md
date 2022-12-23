---
title: Docker数据迁移
date: 2022-12-23 14:52:19
tags:
 - Docker
categories:
 - Docker
---

# Docker 数据迁移
> 主机是lxc的，只分配了10G，安装 office 镜像空间不够，将 Docker 数据迁移到硬盘上。

1. 停止 Docker
    ```
    systemctl stop docker.socket
    systemctl stop docker
    ```
2. 拷贝数据
    ```bash
    cp -a /var/lib/docker /mnt/nas/application/docker
    mv /var/lib/docker /var/lib/docker-old
    ln -s /mnt/nas/application/docker /var/lib/
    ```
3. 启动测试, 删除原数据
    ```bash
    systemctl start docker
    systemctl status docker
    rm -rf /var/lib/docker-old
    ```