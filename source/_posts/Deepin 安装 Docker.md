---
title: Deepin 安装 Docker
date: 2019-08-12 11:34:46
tags:
 - docker
 - deepin
categories:
 - Docker
---

> 最近公司发布会由于是异地举行，被部署搞的很伤，连续加了几天班，大多都是因为运行环境问题，所以这次会后，领导准备全面使用`docker`，将整体服务容器化，后期使用自动部署解决这个问题。我们现在使用的是`Spring Cloud`，比较容易迁移，自己也一直想看看`docker`，在`deepin`上测试一下。

# 安装Docker
根据`deepin`官方`wiki`安装即可： [Deepin安装Docker教程](https://wiki.deepin.org/wiki/Docker)

`Docker` 详细文档：[Docker文档](https://yeasy.gitbooks.io/docker_practice/)

`Docker` 仓库：[https://store.docker.com/](https://store.docker.com/)
**要注意的地方：**

- 添加`docker`仓库的时候，注意更换版本代号，因为`deepin`是基于`debian`的，所以改成`debian`的即可。我的`deepin`是`15.8`，`debian`版为`8.0`，版本号改为`jessie`即可。
 相关命令：

```java
lsb_release -cs
cat /etc/debian_version
sudo add-apt-repository  "deb [arch=amd64] https://download.docker.com/linux/debian jessie stable"
```
![命令](leanote://file/getImage?fileId=5c10bde675a1b808b200005b)

之后就是更新仓库 `sudo apt-get update`， 安装`sudo apt-get install docker-ce` 。

我没有按文档更改`docker.service的unit文件`，直接启动成功～

- `systemctl start docker` - 开启`docker`
- `docker version` - 查看`docker`版本
![title](leanote://file/getImage?fileId=5c10c2ff75a1b808b200005c)

- 提示没有权限读取服务端信息使用`sudo docker version` 即可
![title](leanote://file/getImage?fileId=5c10c4ce75a1b808b200005d)

# 运行测试
- `sudo docker run hello-world` 下载镜像并运行
![title](leanote://file/getImage?fileId=5c10c4f075a1b808b200005e)

# CentOS/RHEL 的用户需要注意的事项
在 Ubuntu/Debian 上有 UnionFS 可以使用，如 aufs 或者 overlay2，而 CentOS 和 RHEL 的内核中没有相关驱动。因此对于这类系统，一般使用 devicemapper 驱动利用 LVM 的一些机制来模拟分层存储。这样的做法除了性能比较差外，稳定性一般也不好，而且配置相对复杂。Docker 安装在 CentOS/RHEL 上后，会默认选择 devicemapper，但是为了简化配置，其 devicemapper 是跑在一个稀疏文件模拟的块设备上，也被称为 loop-lvm。这样的选择是因为不需要额外配置就可以运行 Docker，这是自动配置唯一能做到的事情。但是 loop-lvm 的做法非常不好，其稳定性、性能更差，无论是日志还是 docker info 中都会看到警告信息。官方文档有明确的文章讲解了如何配置块设备给 devicemapper 驱动做存储层的做法，这类做法也被称为配置 direct-lvm。

除了前面说到的问题外，devicemapper + loop-lvm 还有一个缺陷，因为它是稀疏文件，所以它会不断增长。用户在使用过程中会注意到 /var/lib/docker/devicemapper/devicemapper/data 不断增长，而且无法控制。很多人会希望删除镜像或者可以解决这个问题，结果发现效果并不明显。原因就是这个稀疏文件的空间释放后基本不进行垃圾回收的问题。因此往往会出现即使删除了文件内容，空间却无法回收，随着使用这个稀疏文件一直在不断增长。

所以对于 CentOS/RHEL 的用户来说，在没有办法使用 UnionFS 的情况下，一定要配置 direct-lvm 给 devicemapper，无论是为了性能、稳定性还是空间利用率。

或许有人注意到了 CentOS 7 中存在被 backports 回来的 overlay 驱动，不过 CentOS 里的这个驱动达不到生产环境使用的稳定程度，所以不推荐使用。