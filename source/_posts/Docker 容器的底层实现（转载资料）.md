---
title: Docker 容器的底层实现（转载资料）
date: 2019-08-12 11:32:47
tags:
 - docker
categories:
 - Docker
---

写的非常详细，可以直接去原文看： [Docker(1)底层实现](https://www.cnblogs.com/zcqdream/articles/6542236.html)

*以下存档防止丢失～～*

# __Docker底层实现__

Docker并没有传统虚拟化的Hypervisor层,因为dokcer是基于容器技术的轻量级虚拟化,相对于传统的虚拟化，省去了Hypervisor层的开销,而且其虚拟化技术是基于内核的Cgroup和Namespace技术,处理逻辑与内核深度融合,所以在很多方面,docker的性能与物理机非常接近

在通信上,Docker并不会直接与内核交互,它是通过一个更底层的工具Libcontainer与内核交互的,__Libcontainer 是真正意义上的容器引擎,它通过clone系统调用直接创建容器,通过pivot_root系统调用进入容器,且通过直接操作cgroupfs文件实现对资源的管控__

__Docker本身则侧重于处理更上层的业务__

**容器=cgroup+namespace+rootfs+容器引擎(用户态工具LXC)**
　　.Cgroup　　资源控制
　　.Namespace　　访问隔离
　　.rootfs　　文件系统隔离
　　.容器引擎　　生命周期控制

Docker底层的核心技术包括,linux上的名称空间(Namesaces),控制组(Contorl groups)，Union文件系统,和容器格式(Container format)

# __Cgroup是什么__
__它是control groups 的简写,属于linux内核的一个特性,用于限制和隔离一组进程对系统资源的使用__

资源:CPU,内存,block I/O 网络带宽

devices:设备权限控制

cpuset:分配指定cpu和内存节点

cpu：控制cpu占用率

cpuacct:统计CPU使用情况

memory:限制内存的使用上限

freezer:冻结(暂停) Cgroup中的进程

net_cls:配合tc(traffic controller) 限制网络带宽

net_prio:设置进程的网络流量优先级

huge_tlb:限制HugeTLB的使用

perf_event:允许Perf工具基于Cgroup分组做性能检测

## 容器机制:
让某些进程在彼此隔离的名字空间运行,大家虽然都共用一个内核和某些运行时的环境(例如一些系统命令和库)，但是彼此都看不到，都以为系统中只有自己存在

- 名字空间来做权限的隔离控制
- 利用cgroups来做资源分配

### __Cgroup__的接口和使用
1. 挂载cgroupfs
   ```
   mount -t cgroup -o cpuset cpuset /sys/fs/cgroup/
   ```

2. 查看cgroupfs
    ```
    [root@linux-node2 cgroup]# ll /sys/fs/cgroup/
    总用量 0
    -rw-r--r-- 1 root root 0 5月  18 14:08 cgroup.clone_children
    --w--w--w- 1 root root 0 5月  18 14:08 cgroup.event_control
    -rw-r--r-- 1 root root 0 5月  18 14:08 cgroup.procs
    -r--r--r-- 1 root root 0 5月  18 14:08 cgroup.sane_behavior
    -rw-r--r-- 1 root root 0 5月  18 14:08 cpuset.cpu_exclusive
    -rw-r--r-- 1 root root 0 5月  18 14:08 cpuset.cpus
    -rw-r--r-- 1 root root 0 5月  18 14:08 cpuset.mem_exclusive
    -rw-r--r-- 1 root root 0 5月  18 14:08 cpuset.mem_hardwall
    -rw-r--r-- 1 root root 0 5月  18 14:08 cpuset.memory_migrate
    -r--r--r-- 1 root root 0 5月  18 14:08 cpuset.memory_pressure
    -rw-r--r-- 1 root root 0 5月  18 14:08 cpuset.memory_pressure_enabled
    -rw-r--r-- 1 root root 0 5月  18 14:08 cpuset.memory_spread_page
    -rw-r--r-- 1 root root 0 5月  18 14:08 cpuset.memory_spread_slab
    -rw-r--r-- 1 root root 0 5月  18 14:08 cpuset.mems
    -rw-r--r-- 1 root root 0 5月  18 14:08 cpuset.sched_load_balance
    -rw-r--r-- 1 root root 0 5月  18 14:08 cpuset.sched_relax_domain_level
    drwxr-xr-x 2 root root 0 7月   4 15:26 docker
    drwxr-xr-x 4 root root 0 5月  18 14:09 libvirt
    -rw-r--r-- 1 root root 0 5月  18 14:08 notify_on_release
    -rw-r--r-- 1 root root 0 5月  18 14:08 release_agent
    -rw-r--r-- 1 root root 0 5月  18 14:08 tasks
    ```
    可以看到这里有很多的控制文件,其中以cpuset开头的控制文件,都是由cpuset子系统产生的,其他文件则是由Cgroup产生,
    这里的tasks文件记录了这个Cgroup的所有进程，包括线程，在系统启动后,默认没有对Cgroup做任何配置的情况下,cgroupfs只有一个根目录,并且系统所有进程都在这个根目录中，既进程pid都在根目录tasks文件中

3. 创建Cgroup
    ```
    mkdir /sys/fs/cgroup/child
    ```
    这样就创建了一个新的Cgroup

4. 配置Cgroup
    ```
    [root@linux-node2 cgroup]# echo 0 > child/cpuset.cpus
    [root@linux-node2 cgroup]# echo 0 > child/cpuset.mems
    ```
    这样就可以限制这个Cgroup的进程只能在0号CPU上运行,并且只会从0号内存节点分配内存

5. 使能Cgroup
    ```
    [root@linux-node2 child]# echo 2487 > /sys/fs/cgroup/child/tasks 
    
    这里2487代表进程的pid号
    
    [root@linux-node2 child]# echo $$ > /sys/fs/cgroup/child/tasks 也是可以的
    
    这里$$代表当前进程
    
    -----------------
    写入task只会把制定的进程加到child中.
    写入cgroup.procs则会把这个进程所属的整个线程都加到child中
    ```

## Cgroup子系统介绍
1. cpuet子系统
    cpuset可以为一组进程分配指定的CPU和内存节点,cpuset一开始用在高性能计算(HPC)行的.在NUMA架构的服务器上,通过将进程绑定到固定的CPU和内存节点上
    来避免进程在运行时因跨节点内存访问而导致的性能下降,当然,现在cpuset也广泛的用在了kvm和容器上
    - cpuset主要接口:
    ```
    cpuset.cpus: 允许进程使用的CPU列表 (例如:0-4,9)
    cpuset.mems: 允许进程使用的内存节点列表 (例如0-1)
    ```

2. CPU子系统
     cpu子系统用于限制进程的CPU占用率,实际上它有三个功能,分别通过不同的接口来提供
    ```
    CPU比重分配: 这个特性使用的接口是cpu.shares .如果cgroupfs目录下创建了2个Cgroup，分别是C1和C2,并且将cpu.shares分别设置为512和1024,那么当C1和C2争用CPU时,C2将会得到比C1 多一倍的CPU占用率,要值得注意的是,只有当它们争用CPU时,cpu.share才会起作用,如果C2是空闲的,那么C1可以得到全部的CPU资源
    
    CPU带宽限制:这个特性使用的接口是cpu.cfs_period_us和cpu.cfs_quota_us 。这2个接口单位是微秒
    
    实时进程的CPU带宽限制: 使用的是cpu.rt_period_us和cpu.rt__runtime_us
    ```

3. cpuacct子系统
    用来统计个Cgroup的CPU使用情况

4. memory子系统
    限制Cgroup所能使用的内存上限,有如下接口
    - memory.limit_in_bytes: 设定内存上限,单位是字节,也可以使用k/K，m/M或者g/G 表示要设置数值单位
    ```
    echo 1G > memory.limit_in——bytes
      
    如果Cgroup使用的内存超过上限,linux内核会尝试回收内存,如果仍然无法将内存使用量控制在上限之内,系统将会触发OOM，选择并杀死该Cgroup中的某个进程
    ```
    - memory.memsw.limit__in_bytes:设定内存加上交换分区的使用量,通过设置这个值,可以防止进程把交换分区用光
    - memory.oom_control: 如果设置为0，那么在内存使用量超过上限时,系统不会'杀死' 进程,而是阻塞进程直到有内存被释放可供使用时: 另一方面,系统会向用户态发送事件通知,用户态的监控程序可以根据该事件来做相应的处理,例如提高内存上限等
    
5. blkio子系统
用来限制Cgroup的block I/O带宽

6. devices子系统
控制Cgroup的进程对那些设备有访问权限

# 基本架构
Docker采用了C/S架构
客户端和服务端可以运行在一个机器上,也可以通过socket或者RESTful API 来进行通信
![Docker采用了C/S架构](leanote://file/getImage?fileId=5c19179375a1b808b20000a8)
Docker  daemon 一般在宿主机后台运行,等待接收客户端的消息
Docker客户端则为客户提供一系列可执行的命令, 用户使用这些命令跟docker daemon交互

# 剖析
## 名字空间
　　名字空间是linux内核一个强大的特性，每个容器都有自己单独的名字空间,运行在其中的应用都像是在独立的操作系统一样。名字空间保证了各容器之前互不影响

## pid名字空间
　　不同用户的进程就是通过pid名字空间隔离开来的,且不同名字空间中可以有相同的pid。所有的LXC进程在Dokcer中的父进程为dokcer进程

每个LXC(__基于容器的操作系统层级的虚拟化技术__)进程具有不同的名字空间,同时由于永许嵌套,因此可以很方便的实现嵌套的Docker容器

## net名字空间
　　网络端口还是共享的host端口 ，网络隔离是通过net名字空间实现的, 每个net名字空间有独立的网络设置,IP地址,路由表,/proc/net 目录(__proc文件系统是一个伪文件系统，它只存在内存当中，而不占用外存空间。它以文件系统的方式为访问系统内核数据的操作提供接口__)。这样每个容器就隔离开来。Docker默认采用veth方式(成对出现的点对点网络设置)【==懵逼状态。。】 将容器中的虚拟网卡同host上的一个Dokcer网桥docker0连接在一起

[软连：http://www.open-open.com/lib/view/open1488423458438.html](http://www.open-open.com/lib/view/open1488423458438.html)

## ipc名字空间
　　容器中进程交互还是采用了linux常见的进程交互方法，包括信号量，消息队列,共享内存等，容器的进程间交互实际上还是host上具有相同pid名字空间中的进程间交互,因此需要在ipc资源申请时加入名字空间信息,每个IPC资源有一个唯一的32位ID

## mnt名字空间
　　类似chroot，将一个进程放到一个特定的目录执行,mnt名字空间允许不同名字空间的进程看到文件结构，这样每个名字空间中的进程所看到的文件目录就被隔离开来了。每个名字空间中的容器在 /proc/mounts的信息只包含所在名字空间的mount point

## uts名字空间
　　UTS名字空间允许每个容器拥有独立的hostname和domain name 使其在网络上可以被视作一个独立的节点而非主机上的一个进程

## USER 名字空间
　　每个容器可以有不同的用户和组ID，也就是说可以在容器内用容器内部的用户执行程序而非主机上的用户

## 控制组
　　控制组(cgroups)是linux内核的一个特性，主要用来对共享资源进行隔离,限制,审计等.只有能控制分配到容器的资源,才能避免当多个容器同时运行时对系统资源的竞争

__linux内核2.6.24开始支持__

控制组可以提供对容器内的 内存，CPU,磁盘IO等资源的限制和计费管理

## Union文件系统
　　union文件系统(unionFS)是一种分层，轻量级并且高性能的文件系统,它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下

　　union文件系统是docker镜像的基础,镜像可以通过分层来进行继承，基于基础镜像(没有父镜像)，可以制作各种具有应用镜像

另外，不同docker容器就可以共享一些基础的文件系统层,同时再加上自己独有的改动层，大大提高了存储的效率

Docker中使用的AUFS 就是一种union FS。 AUFS支持为每一个成员目录（类似git的分支）设定 只读，读写，写出 权限，同时AUFS里有一个类似分层的概念，对只读权限的分支可以逻辑上进行增量的修改(不影响只读部分)

Docker目前支持的union文件系统种类包括 AUFS,btrfs，vfs和DeviceMapper

## 容器格式
最初，docker采用了LXC中的容器格式，__自1.20版本开始,Docker也开始支持新的libcontainer格式，并作业默认选项__

## Docker网络实现
Docker 的网络实现其实就是利用了 Linux 上的网络名字空间和虚拟网络设备（特别是 veth pair）。建议先
熟恲了解返两部分的基本概念再阅读本章。
基本原理
首先，要实现网络通信，机器需要至少一个网络接口（物理接口戒虚拟接口）来收发数据包；此外，如果
丌同子网乀间要迕行通信，需要路由机制。
Docker 中的网络接口默讣都是虚拟的接口。虚拟接口的优势乀一是转发效率较高。 Linux 通过在内核中迕
行数据复制来实现虚拟接口乀间的数据转发，发送接口的发送缓存中的数据包被直接复制到接收接口的接
收缓存中。对亍本地系统和容器内系统看来就像是一个正常的以太网卡，叧是它丌需要真正同外部网络设
备通信，速度要徆快。
Docker 容器网络就利用了返项技术。它在本地主机和容器内分别创建一个虚拟接口，幵讥它们彼此连通
（返样的一对接口叨做 veth pair ）。

### veth pair　　
![veth pair](leanote://file/getImage?fileId=5c19179375a1b808b20000a7)

## 镜像的实现原理
Docker 镜像是怎举实现增量的修改和维护的？ 每个镜像都由徆多层次极成，Docker 使用 Union FS 将返
些丌同的层结合到一个镜像中去。
通常 Union FS 有两个用途, 一方面可以实现丌借劣 LVM、RAID 将多个 disk 挂到同一个目录下,另一个更
常用的就是将一个叧读的分支和一个可写的分支联合在一起，Live CD 正是基亍此方法可以允许在镜像丌
变的基础上允许用户在其上迕行一些写操作。 Docker 在 AUFS 上极建的容器也是利用了类似的原理。

## Docker 容器
容器是 Docker 又一核心概念。
简单的说，容器是独立运行的一个戒一组应用，以及它们的运行态环境。对应的，虚拟机可以理解为模拟
运行的一整套操作系统（提供了运行态环境和其他系统环境）和跑在上面的应用。

当操作docker run 内部实现
docker run 来创建容器时，Docker 在后台运行的标准操作包括：
检查本地是否存在指定的镜像，不存在就从公有仓库下载
利用镜像创建并启动一个容器
分配一个文件系统，并在叧读的镜像层外面挂载一层可读写层
从宿主主机配置的网桥接口中桥接一个虚拟接口到容器中去
从地址池配置一个 ip 地址给容器
执行用户挃定的应用程序
执行完毕后容器被终止

## 数据卷
数据卷是一个可供一个戒多个容器使用的特殊目录，它绕过 UFS，可以提供徆多有用的特性：
数据卷可以在容器之间共享和重用
对数据卷的修改会立马生效
对数据卷的更新，不会影响镜像
卷会一直存在，直到没有容器使用

---数据卷的使用。类似于linux下对目录的mount

使用 -v 标记来创建一个数据卷幵挂载到容器里。在一次 run 中多次使用可以挂载多个数据卷。
```
docker run -d -P --name web -v /webapp training/webapp python app.py
```
使用 -v 标记也可以挃定挂载一个本地主机的目录到容器中去。
```
 docker run -d -P --name web -v /src/webapp:/opt/webapp training/webapp python app.py
```
加载主机的 /src/webapp 目录到容器的 /opt/webapp 目录，

本地目录的路径必须是绝对路径，如果目录不存在 Docker 会自动为你创建它。

Docker 挂载数据卷的默讣权限是读写，用户也可以通过 :ro 指定为只读
```
docker run -d -P --name web -v /src/webapp:/opt/webapp:ro training/webapp python app.py
```