---
title: Docker 常用命令
date: 2019-08-12 11:35:42
tags:
 - docker
categories:
 - Docker
---

#__shell__
- `systemctl start docker` - 启动
- `service docker restart` - 重启
- `docker version` - 查看运行状态
- `/etc/docker/daemon.json` - 镜像源文件
```
`vim /etc/docker/daemon.json` 
{
  "registry-mirrors": ["https://registry.docker-cn.com"]
}
```

# 镜像相关
- `docker image ls` - 列出已下载的镜像
- `docker image ls -f dangling=true` - 查看虚悬镜像， 有的镜像更新后可能会出现既没有仓库名，也没有标签，均为 <none>，称为虚悬镜像，可用此命令查看
- `docker image prune` - 一般来说，虚悬镜像已经失去了存在的价值，是可以随意删除的，可以用此命令删除。
- `docker rm $(docker ps -aq)` - 删除所有镜像
- `docker rm -v` - 删除镜像时同事删除其数据卷
- `docker rmi $(docker images -q)` - 删除所有镜像
- `docker rmi -f 127.0.0.1:8082/weiba/nginx:weiba` - 删除指定tag容器
- `docker save 镜像id > tomcat8-apr.tar` - 导出镜像
- `docker load < tomcat8-apr.tar` - 导入镜像
    ```
    镜像和容器 导出和导入的区别
    镜像导入和容器导入的区别：
    1）容器导入 是将当前容器 变成一个新的镜像
    2）镜像导入 是复制的过程
    save 和 export区别：
    1）save 保存镜像所有的信息-包含历史
    2）export 只导出当前的信息
    ```

- `docker history 90457edaf6ff` - 查看镜像/容器历史操作记录

# 容器相关
- `docker container rm  trusting_newton` - 清理指定的处于终止状态的容器
- `docker container prune` - 清理所有处于终止状态的容器
- `docker container start xxx` - 重新启动终止的容器
- `docker ps` - 查看运行中的容器
- `docker container ls` - 查看运行的容器状态
- `docker container ls -a` - 查看所有已经创建的包括终止状态的容器
- `docker inspect web` - 查看`web`容器的信息
- `docker stop 容器id`&&`docker container stop xxx` - 停止指定容器, 在docker stop命令执行的时候，会先向容器中PID为1的进程发送系统信号SIGTERM，然后等待容器中的应用程序终止执行，如果等待时间达到设定的超时时间，或者默认的10秒，会继续发送SIGKILL的系统信号强行kill掉进程。
- `docker kill xxx`&&`docker container kill xxx` - 立刻停止容器，该命令不会等待容器中的应用终止。
- `docker stop $(docker ps -aq)` - 停止所有容器
- `docker restart 容器id` - 重启指定容器
- `docker export b91d9ad83efa > tomcat80824.tar` - 导入容器
- `docker import tomcat80824.tar` - 导入容器
- `docker import http://example.com/exampleimage.tgz example/imagerepo` - 从网络导入

    ```
    注：用户既可以使用 docker load 来导入镜像存储文件到本地镜像库，也可以使用 docker import 来导入一个容器快照到本地镜像库。这两者的区别在于容器快照文件将丢弃所有的历史记录和元数据信息（即仅保存容器当时的快照状态），而镜像存储文件将保存完整记录，体积也要大。此外，从容器快照文件导入时可以重新指定标签等元数据信息。
    ```

## 容器运行相关
- `docker exec -i -t 0a4f bash` - 运行/进入 容器，使用exec方式运行容器内的bash，在其中exit容器不会停止，使用run方式运行的容器，exit后容器会停止
- `docker inspect --format  "{{.State.Pid}}" nexus3` - 查看容器pid
- `nsenter --target PID --mount --uts --ipc --net --pid` - 根据pid进入容器
- `docker run hello-world` - 使用`docker`运行`hello-world`, 如果运行的`image`不存在会自动从仓库下载并运行。
- `docker run -p 22:22 -p 1080:1080 nginx` - 给容器映射多个端口 
- `docker run -d ubuntu:18.04 /bin/sh -c "while true; do echo hello world; sleep 1; done"` - 
```
-d 守护状态运行容器，此时容器会在后台运行并不会把输出的结果 (STDOUT) 打印到宿主机上面(输出结果可以用 docker logs 查看)。**容器是否会长久运行，是和 docker run 指定的命令有关，和 -d 参数无关。**
```
- `docker run -it ubuntu` - 在运行时可以指定新的命令来替代镜像设置中的这个默认命令，比如，ubuntu 镜像默认的 CMD 是 /bin/bash，如果我们直接 docker run -it ubuntu 的话，会直接进入 bash。
- `docker run -it ubuntu bash` - 使用`docker`运行`Ubuntu`并执行`bash`命令：
 ![title](leanote://file/getImage?fileId=5c18fd1375a1b808b200009e)
> -it：这是两个参数，一个是 -i：交互式操作，一个是 -t 终端。我们这里打算进入 bash 执行一些命令并查看返回结果，因此我们需要交互式终端。
> --rm：这个参数是说容器退出后随之将其删除。默认情况下，为了排障需求，退出的容器并不会立即删除，除非手动 docker rm。我们这里只是随便执行个命令，看看结果，不需要排障和保留结果，因此使用 --rm 可以避免浪费空间。
> ubuntu：这是指用 ubuntu 镜像为基础来启动容器。
> bash：放在镜像名后的是命令，这里我们希望有个交互式 Shell，因此用的是 bash。

- `docker run -it ubuntu cat /etc/os-release` - 我们也可以在运行时指定运行别的命令，如 docker run -it ubuntu cat /etc/os-release。这就是用 cat /etc/os-release 命令替换了默认的 /bin/bash 命令了，输出了系统版本信息。
- `docker run -it --rm --name test centos /bin/bash` - 退出后自己删除

# 日志相关
- `docker logs xxx` - 查看容器运行时输出日志
- `docker logs -f xxx` - 滚动查看日志

# 仓库相关
- `docker login 127.0.0.1:8082` - 登录仓库
- `docker pull 127.0.0.1:8082/weiba/nginx:weiba` - 下载镜像到本地
- `docker pull 127.0.0.1:8082/weiba/nginx:weiba` - - 推送镜像到`127.0.0.1:8082` 仓库
- `docker search 127.0.0.1:8082/weiba` - v1 API 直接docker搜索私有仓库镜像，**v2 API 不支持搜索**
- `curl 127.0.0.1:8082/v2/_catalog` - v2 API 查看私有Nexus3 Docker仓库镜像列表
- `curl 127.0.0.1:8082/v2/ubuntu/tags/list` - v2 API Nexus3 Docker 查看`ubuntu`镜像标签列表

# 数据卷相关
- `docker volume ls` - 查看数据卷
- `docker volume create my-vol` - 创建一个`my-vol`数据卷
- `docker volume rm my-vol` - 删除数据卷
- `docker volume inspect my-vol` - 查看数据卷信息
- `docker volume prune` - 删除无主数据卷
- `docker run -d -P --name web --mount type=bind,source=/src/webapp,target=/opt/webapp training/webapp python app.py`
    - 使用--mount将主机/src/webapp目录挂载到容器的/opt/webapp目录。
      主机目录不存在时会报错,可以使用`-v /src/webapp:/opt/webapp`替换`--mount type=bind,source=/src/webapp,target=/opt/webapp`参数，如果本地目录不存在 Docker 会自动为你创建一个文件夹
      
    - Docker 挂载主机目录的默认权限是 读写，用户也可以通过增加 readonly 指定为 只读

        ```
        --mount type=bind,source=/src/webapp,target=/opt/webapp,readonly
        ```
- `docker run --rm -it --mount type=bind,source=$HOME/.bash_history,target=/root/.bash_history ubuntu:18.04 bash` - 挂载单个文件到容器，**这是历史记录文件，创建后可以记录在容器中的操作哦**
    - `-v $HOME/.bash_history:/root/.bash_history ` - 文件不存在时自动创建文件
- 宿主机与容器拷贝文件

    ```
    1.docker ps -a  查看对应容器的ID
    2.docker inspect -f "{{.Id}}" d4f6bfca36fa   查到容器的存储ID值
    3.接下来直接cp就行
    cp elasticsearch-5.2.1.rpm /software/data/docker/devicemapper/mnt/d4f6bfca36faa70276a40dd560d8a0d29b2fa5f3f8d6acc2189d185ac1f5b1c3/rootfs/root/
    ```

# Tag 相关
- `docker tag nginx:latest 127.0.0.1:8082/weiba/nginx:weiba` - 生成tag

# 网络相关
- `docker run -d -P(大写) training/webapp python app.py` - 随机映射一个 49000~49900 的端口到内部容器开放的网络端口

- `-p(小写)` 参数格式 - ip:hostPort:containerPort | ip::containerPort | hostPort:containerPort， 该标记可以多次使用来绑定多个端口

- `docker run -d -p 5000:5000 training/webapp python app.py` - 使用 hostPort:containerPort 格式本地的 5000 端口映射到容器的 5000 端口
- `docker run -d -p 127.0.0.1:5000:5000 training/webapp python app.py` - 使用 ip:hostPort:containerPort 格式指定映射使用一个特定地址，比如 localhost 地址 127.0.0.1
- `docker run -d -p 127.0.0.1::5000 training/webapp python app.py` - 使用 ip::containerPort 绑定 localhost 的任意端口到容器的 5000 端口，本地主机会自动分配一个端口。
- `docker run -d -p 127.0.0.1:5000:5000/udp training/webapp python app.py` - 还可以使用 udp 标记来指定 udp 端口
- `docker port 8a256` - 根据镜像名称或id查看映射的端口配置
- `docker port 8a256 8081 - 查看8081端口绑定的地址

## 容器互联
- `docker network create -d bridge my-net` - 创建一个新的 Docker 网络。
    - -d 参数指定 Docker 网络类型，有 bridge overlay。其中 overlay 网络类型用于 [Swarm mode](https://yeasy.gitbooks.io/docker_practice/swarm_mode)。
    - `docker run -it --rm --name busybox1 --network my-net busybox sh` - 运行一个容器并连接到新建的 my-net 网络
    - `docker run -it --rm --name busybox2 --network my-net busybox sh` - 打开新的终端，再运行一个容器并加入到 my-net 网络
    - 在 busybox1 容器输入以下 `ping busybox2`
    - 在 busybox2 容器执行 `ping busybox1`
    - 即可发现已可以互相通过内网地址通信

##配置DNS
如何自定义配置容器的主机名和 DNS 呢？秘诀就是 Docker 利用虚拟文件来挂载容器的 3 个相关配置文件。

在容器中使用 mount 命令可以看到挂载信息：
```
$ mount
/dev/disk/by-uuid/1fec...ebdf on /etc/hostname type ext4 ...
/dev/disk/by-uuid/1fec...ebdf on /etc/hosts type ext4 ...
tmpfs on /etc/resolv.conf type tmpfs ...
```
这种机制可以让宿主主机 DNS 信息发生更新后，所有 Docker 容器的 DNS 配置通过 /etc/resolv.conf 文件立刻得到更新。

配置全部容器的 DNS ，也可以在 /etc/docker/daemon.json 文件中增加以下内容来设置。
```
{
  "dns" : [
    "114.114.114.114",
    "8.8.8.8"
  ]
}
```
这样每次启动的容器 DNS 自动配置为 114.114.114.114 和 8.8.8.8。使用以下命令来证明其已经生效。
```
$ docker run -it --rm ubuntu:18.04  cat etc/resolv.conf

nameserver 114.114.114.114
nameserver 8.8.8.8
```

如果用户想要手动指定容器的配置，可以在使用 docker run 命令启动容器时加入如下参数：

-h HOSTNAME 或者 --hostname=HOSTNAME 设定容器的主机名，它会被写到容器内的 /etc/hostname 和 /etc/hosts。但它在容器外部看不到，既不会在 docker container ls 中显示，也不会在其他的容器的 /etc/hosts 看到。

--dns=IP_ADDRESS 添加 DNS 服务器到容器的 /etc/resolv.conf 中，让容器用这个服务器来解析所有不在 /etc/hosts 中的主机名。

--dns-search=DOMAIN 设定容器的搜索域，当设定搜索域为 .example.com 时，在搜索一个名为 host 的主机时，DNS 不仅搜索 host，还会搜索 host.example.com。

__注意：如果在容器启动时没有指定最后两个参数，Docker 会默认用主机上的 /etc/resolv.conf 来配置容器。__

# 列出镜像
- `docker image ls` - 列出已下载的镜像
![title](leanote://file/getImage?fileId=5c18fd1375a1b808b2000099)

- `docker system df` - 查看镜像、容器、数据卷所占用的空间

- `docker image ls -f dangling=true` - 查看虚悬镜像， 有的镜像更新后可能会出现既没有仓库名，也没有标签，均为 <none>，称为虚悬镜像，可用此命令查看
- `docker image prune` - 一般来说，虚悬镜像已经失去了存在的价值，是可以随意删除的，可以用下面的命令删除。

- `docker image ls -a` - 中间层镜像，为了加速镜像构建、重复利用资源，Docker 会利用 中间层镜像。所以在使用一段时间后，可能会看到一些依赖的中间层镜像。默认的 docker image ls 列表中只会显示顶层镜像，如果希望显示包括中间层镜像在内的所有镜像的话，需要加 -a 参数。

不加任何参数的情况下，docker image ls 会列出所有顶级镜像，但是有时候我们只希望列出部分镜像。docker image ls 有好几个参数可以帮助做到这个事情。

- `docker image ls ubuntu` - 根据仓库名列出镜像
- `docker image ls ubuntu:18.04` - 列出特定的某个镜像，也就是说指定仓库名和标签
- `docker image ls -f since=mongo:3.2` - 除此以外，docker image ls 还支持强大的过滤器参数 --filter，或者简写 -f。之前我们已经看到了使用过滤器来列出虚悬镜像的用法，它还有更多的用法。比如，我们希望看到在 mongo:3.2 之后建立的镜像，想查看某个位置之前的镜像也可以，只需要把 since 换成 before 即可。
- `docker image ls -f label=com.example.version=0.1` - 如果镜像构建时，定义了 LABEL，还可以通过 LABEL 来过滤。

# 以特定格式显示
默认情况下，docker image ls 会输出一个完整的表格，但是我们并非所有时候都会需要这些内容。比如，刚才删除虚悬镜像的时候，我们需要利用 docker image ls 把所有的虚悬镜像的 ID 列出来，然后才可以交给 docker image rm 命令作为参数来删除指定的这些镜像，这个时候就用到了 -q 参数。
- `docker image ls -q` - 列出镜像id
![title](leanote://file/getImage?fileId=5c18fd1375a1b808b200009d)
- `docker image ls --format "{{.ID}}: {{.Repository}}"` - 列出镜像，只包含镜像ID和仓库名
![title](leanote://file/getImage?fileId=5c18fd1375a1b808b20000a2)
- `sudo docker image ls --format "{{.ID}}:{{.Repository}}:{{.Size}}"` 
![title](leanote://file/getImage?fileId=5c18fd1375a1b808b20000a3)
- `docker image ls --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"` - 以表格等距显示，并且有标题行，和默认一样，不过自己定义列
![title](leanote://file/getImage?fileId=5c18fd1375a1b808b200009b)

# 删除本地镜像
- `docker image rm [选项] <镜像1> [<镜像2> ...]` - 删除本地的镜像
- `docker image rm $(docker image ls -q redis)` - 删除所有名为`redis`的镜像
- `docker image rm $(docker image ls -q -f before=mongo:3.2)` - 删除所有在 mongo:3.2 之前的镜像

# commit 修改镜像， 一般不使用commit修改镜像
1. 运行`nginx`镜像
- `docker run --name webserver -d -p 80:80 nginx` 

2.  修改`nginx`启动页面
- `docker exec -it webserver bash` - 进入`webserver`镜像
- `echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html` - 修改启动页面，刷新即可看到。
     - 修改后的镜像不`commit`，镜像重启后会恢复原样。

3. 查看镜像修改内容
- `docker diff webserver` - 退出镜像后可以查看修改的记录
![title](leanote://file/getImage?fileId=5c18fd1375a1b808b200009f)

4. 保存修改生成新的镜像
- `docker commit --author "weiba <test@gmail.com>" --message "修改nginx默 认网页" webserver nginx:v2` -- 生成新的镜像，--author 是指定修改的作者，而 --message 则是记录本次修改的内容。这点和 git 版本控制相似，不过这里这些信息可以省略留空。
![title](leanote://file/getImage?fileId=5c18fd1375a1b808b20000a0)

5. 查看镜像操作记录，可以查看刚刚的commit
- `docker history nginx:v2` - 查看镜像的操作记录
![title](leanote://file/getImage?fileId=5c18fd1375a1b808b2000098)

6. 启动刚刚修改的镜像
- `docker run --name web2 -d -p 81:80 nginx:v2` - 启动镜像

# 慎用 docker commit
使用 docker commit 命令虽然可以比较直观的帮助理解镜像分层存储的概念，但是实际环境中并不会这样使用。

首先，如果仔细观察之前的 docker diff webserver 的结果，你会发现除了真正想要修改的 /usr/share/nginx/html/index.html 文件外，由于命令的执行，还有很多文件被改动或添加了。这还仅仅是最简单的操作，如果是安装软件包、编译构建，那会有大量的无关内容被添加进来，如果不小心清理，将会导致镜像极为臃肿。

此外，使用 docker commit 意味着所有对镜像的操作都是黑箱操作，生成的镜像也被称为黑箱镜像，换句话说，就是除了制作镜像的人知道执行过什么命令、怎么生成的镜像，别人根本无从得知。而且，即使是这个制作镜像的人，过一段时间后也无法记清具体在操作的。虽然 docker diff 或许可以告诉得到一些线索，但是远远不到可以确保生成一致镜像的地步。这种黑箱镜像的维护工作是非常痛苦的。

而且，回顾之前提及的镜像所使用的分层存储的概念，除当前层外，之前的每一层都是不会发生改变的，换句话说，任何修改的结果仅仅是在当前层进行标记、添加、修改，而不会改动上一层。如果使用 docker commit 制作镜像，以及后期修改的话，每一次修改都会让镜像更加臃肿一次，所删除的上一层的东西并不会丢失，会一直如影随形的跟着这个镜像，即使根本无法访问到。这会让镜像更加臃肿。

# 使用 Dockerfile 定制镜像
在一个空白目录中，建立一个文本文件，并命名为 Dockerfile：
```shell
$ mkdir mynginx
$ cd mynginx
$ touch Dockerfile
```
其内容为：
```
FROM nginx
RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
```

这个 Dockerfile 很简单，一共就两行。涉及到了两条指令，FROM 和 RUN。

## FROM 指定基础镜像
所谓定制镜像，那一定是以一个镜像为基础，在其上进行定制。就像我们之前运行了一个 nginx 镜像的容器，再进行修改一样，基础镜像是必须指定的。而 FROM 就是指定基础镜像，因此一个 Dockerfile 中 FROM 是必备的指令，并且必须是第一条指令。

除了选择现有镜像为基础镜像外，Docker 还存在一个特殊的镜像，名为 scratch。这个镜像是虚拟的概念，并不实际存在，它表示一个空白的镜像。
```shell
FROM scratch
...
```
如果你以 scratch 为基础镜像的话，意味着你不以任何镜像为基础，接下来所写的指令将作为镜像第一层开始存在。

## RUN 执行命令
RUN 指令是用来执行命令行命令的。由于命令行的强大能力，RUN 指令在定制镜像时是最常用的指令之一。其格式有两种：

- `shell` 格式：RUN <命令>，就像直接在命令行中输入的命令一样。刚才写的 Dockerfile 中的 RUN 指令就是这种格式。
```shell
RUN echo '<h1>Hello, Docker!</h1>' > /usr/share/nginx/html/index.html
```
- `exec` 格式：RUN ["可执行文件", "参数1", "参数2"]，这更像是函数调用中的格式。

既然 `RUN` 就像 `Shell` 脚本一样可以执行命令，那么我们是否就可以像 `Shell` 脚本一样把每个命令对应一个 `RUN` 呢？比如这样：
```Dockerfile
FROM debian:jessie

RUN apt-get update
RUN apt-get install -y gcc libc6-dev make
RUN wget -O redis.tar.gz "http://download.redis.io/releases/redis-3.2.5.tar.gz"
RUN mkdir -p /usr/src/redis
RUN tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1
RUN make -C /usr/src/redis
RUN make -C /usr/src/redis install
```
之前说过，**Dockerfile 中每一个指令都会建立一层，RUN 也不例外。每一个 RUN 的行为，就和刚才我们手工建立镜像的过程一样：新建立一层，在其上执行这些命令，执行结束后，commit 这一层的修改，构成新的镜像。**
而上面的这种写法，创建了 7 层镜像。这是完全没有意义的，而且很多运行时不需要的东西，都被装进了镜像里，比如编译环境、更新的软件包等等。结果就是产生非常臃肿、非常多层的镜像，不仅仅增加了构建部署的时间，也很容易出错。 这是很多初学 Docker 的人常犯的一个错误。

**Union FS 是有最大层数限制的，比如 AUFS，曾经是最大不得超过 42 层，现在是不得超过 127 层。**

上面的 `Dockerfile` 正确的写法应该是这样：
```Dockerfile
FROM debian:jessie

RUN buildDeps='gcc libc6-dev make' \
    && apt-get update \
    && apt-get install -y $buildDeps \
    && wget -O redis.tar.gz "http://download.redis.io/releases/redis-3.2.5.tar.gz" \
    && mkdir -p /usr/src/redis \
    && tar -xzf redis.tar.gz -C /usr/src/redis --strip-components=1 \
    && make -C /usr/src/redis \
    && make -C /usr/src/redis install \
    && rm -rf /var/lib/apt/lists/* \
    && rm redis.tar.gz \
    && rm -r /usr/src/redis \
    && apt-get purge -y --auto-remove $buildDeps
```

首先，之前所有的命令只有一个目的，就是编译、安装 redis 可执行文件。因此没有必要建立很多层，这只是一层的事情。因此，这里没有使用很多个 RUN 对一一对应不同的命令，而是仅仅**使用一个 RUN 指令，并使用 && 将各个所需命令串联起来。将之前的 7 层，简化为了 1 层。在撰写 Dockerfile 的时候，要经常提醒自己，这并不是在写 Shell 脚本，而是在定义每一层该如何构建。**

并且，这里为了格式化还进行了换行。**Dockerfile 支持 Shell 类的行尾添加 \ 的命令换行方式，以及行首 # 进行注释的格式。**良好的格式，比如换行、缩进、注释等，会让维护、排障更为容易，这是一个比较好的习惯。

此外，还可以看到这一组命令的最后添加了清理工作的命令，删除了为了编译构建所需要的软件，清理了所有下载、展开的文件，并且还清理了 apt 缓存文件。**这是很重要的一步，我们之前说过，镜像是多层存储，每一层的东西并不会在下一层被删除，会一直跟随着镜像。因此镜像构建时，一定要确保每一层只添加真正需要添加的东西，任何无关的东西都应该清理掉。**

## 构建镜像
在 `Dockerfile` 文件所在目录执行: `$ docker build -t nginx:v3 .`, 注意后面的一个点，代表当前文件夹，**这是在指定上下文路径**
![title](leanote://file/getImage?fileId=5c18fd1375a1b808b20000a1)
启动测试：`docker run --name webserver -d -p 80:80 nginx:v3`
![title](leanote://file/getImage?fileId=5c18fd1375a1b808b200009a)
![title](leanote://file/getImage?fileId=5c18fd1375a1b808b200009c)

### 什么是上下文？
首先我们要理解 docker build 的工作原理。**Docker 在运行时分为 Docker 引擎（也就是服务端守护进程）和客户端工具**。Docker 的引擎提供了一组 REST API，被称为 Docker Remote API，而如 docker 命令这样的客户端工具，则是通过这组 API 与 Docker 引擎交互，从而完成各种功能。因此，**虽然表面上我们好像是在本机执行各种 docker 功能，但实际上，一切都是使用的远程调用形式在服务端（Docker 引擎）完成**。也因为这种 C/S 设计，让我们操作远程服务器的 Docker 引擎变得轻而易举。

当我们进行镜像构建的时候，并非所有定制都会通过 RUN 指令完成，经常会需要将一些本地文件复制进镜像，比如通过 COPY 指令、ADD 指令等。而 docker build 命令构建镜像，其实并非在本地构建，而是在服务端，也就是 Docker 引擎中构建的。那么在这种客户端/服务端的架构中，如何才能让服务端获得本地文件呢？

这就引入了上下文的概念。**当构建的时候，用户会指定构建镜像上下文的路径，docker build 命令得知这个路径后，会将路径下的所有内容打包，然后上传给 Docker 引擎。这样 Docker 引擎收到这个上下文包后，展开就会获得构建镜像所需的一切文件**。

如果在 Dockerfile 中这么写：
```dockerfile
COPY ./package.json /app/
```
这并不是要复制执行 docker build 命令所在的目录下的 package.json，也不是复制 Dockerfile 所在目录下的 package.json，而是**复制 上下文（context） 目录下的 package.json**。

因此，COPY 这类指令中的源文件的路径都是相对路径。这也是初学者经常会问的为什么 COPY ../package.json /app 或者 COPY /opt/xxxx /app 无法工作的原因，因为**这些路径已经超出了上下文的范围，Docker 引擎无法获得这些位置的文件**。如果真的需要那些文件，应该将它们复制到上下文目录中去。

一般来说，应该会将 Dockerfile 置于一个空目录下，或者项目根目录下。如果该目录下没有所需文件，那么应该把所需文件复制一份过来. **如果目录下有些东西确实不希望构建时传给 Docker 引擎，那么可以用.gitignore 一样的语法写一个 .dockerignore，该文件是用于剔除不需要作为上下文传递给 Docker 引擎的**。

那么为什么会有人误以为 . 是指定 Dockerfile 所在目录呢？**这是因为在默认情况下，如果不额外指定 Dockerfile 的话，会将上下文目录下的名为 Dockerfile 的文件作为 Dockerfile**。

这只是默认行为，实际上 Dockerfile 的文件名并不要求必须为 Dockerfile，而且并不要求必须位于上下文目录中，比如可以用 -f ../Dockerfile.php 参数指定某个文件作为 Dockerfile： `docker build -t nginx:v4 -f test .`

当然，一般大家习惯性的会使用默认的文件名 Dockerfile，以及会将其置于镜像构建上下文目录中。

## 其它 docker build 的用法
- 直接用 Git repo 进行构建
docker build 还支持从 URL 构建，比如可以直接从 Git repo 中构建：
```
$ docker build https://github.com/twang2218/gitlab-ce-zh.git#:8.14
docker build https://github.com/twang2218/gitlab-ce-zh.git\#:8.14
Sending build context to Docker daemon 2.048 kB
Step 1 : FROM gitlab/gitlab-ce:8.14.0-ce.0
8.14.0-ce.0: Pulling from gitlab/gitlab-ce
aed15891ba52: Already exists
773ae8583d14: Already exists
...
```
这行命令指定了构建所需的 Git repo，并且指定默认的 master 分支，构建目录为 /8.14/，然后 Docker 就会自己去 git clone 这个项目、切换到指定分支、并进入到指定目录后开始构建。

- 用给定的 tar 压缩包构建
```
$ docker build http://server/context.tar.gz
```

- 从标准输入中读取 Dockerfile 进行构建
```
docker build - < Dockerfile
```
或
```
cat Dockerfile | docker build -
```
如果标准输入传入的是文本文件，则将其视为 Dockerfile，并开始构建。这种形式由于直接从标准输入中读取 Dockerfile 的内容，它没有上下文，因此不可以像其他方法那样可以将本地文件 COPY 进镜像之类的事情。

- 从标准输入中读取上下文压缩包进行构建
```
$ docker build - < context.tar.gz
```
如果发现标准输入的文件格式是 gzip、bzip2 以及 xz 的话，将会使其为上下文压缩包，直接将其展开，将里面视为上下文，并开始构建。

## docker save 和 docker load
- 保存镜像
使用 docker save 命令可以将镜像保存为归档文件。
比如我们希望保存这个 alpine 镜像。
```
$ docker save alpine | gzip > alpine-latest.tar.gz
```
然后我们将 alpine-latest.tar.gz 文件复制到了到了另一个机器上，可以用下面这个命令加载镜像：
```
$ docker load -i alpine-latest.tar.gz
Loaded image: alpine:latest
```
如果我们结合这两个命令以及 ssh 甚至 pv 的话，利用 Linux 强大的管道，我们可以写一个命令完成从一个机器将镜像迁移到另一个机器，并且带进度条的功能：
```
docker save <镜像名> | bzip2 | pv | ssh <用户名>@<主机名> 'cat | docker load'
```
# 进入容器
在使用 -d 参数时，容器启动后会进入后台。

某些时候需要进入容器进行操作，包括使用 docker attach 命令或 docker exec 命令，推荐大家使用 docker exec 命令，原因会在下面说明。

- attach 命令
docker attach 是 Docker 自带的命令。下面示例如何使用该命令。
```
root@weiba-PC:/home/weiba/mynginx# docker run -dit ubuntu
02d28e91c81bbd565942c1cc7ec17d9fc46265c47c0e82a78df6e465cdcf77b8
root@weiba-PC:/home/weiba/mynginx# docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
02d28e91c81b        ubuntu              "/bin/bash"         14 seconds ago      Up 12 seconds                           wonderful_torvalds
root@weiba-PC:/home/weiba/mynginx# docker attach 02d
root@02d28e91c81b:/# exit
exit
root@weiba-PC:/home/weiba/mynginx# docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```
注意： **如果从这个 stdin 中 exit，会导致容器的停止。**

-  exec 命令
-i -t 参数
docker exec 后边可以跟多个参数，这里主要说明 -i -t 参数。

**只用 -i 参数时，由于没有分配伪终端，界面没有我们熟悉的 Linux 命令提示符，但命令执行结果仍然可以返回。**

**当 -i -t 参数一起使用时，则可以看到我们熟悉的 Linux 命令提示符。**
```
root@weiba-PC:/home/weiba/mynginx# docker run -dit ubuntu
0a4f6cf3ef619bfa82d0671defed695a305653ce10ec347c9551fc062dbcf6e4
root@weiba-PC:/home/weiba/mynginx# docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
0a4f6cf3ef61        ubuntu              "/bin/bash"         11 seconds ago      Up 10 seconds                           fervent_minsky
root@weiba-PC:/home/weiba/mynginx# docker exec -i 0a4f bash
ls
bin
boot
dev
etc
home
lib
lib64
media
mnt
opt
proc
root
run
sbin
srv
sys
tmp
usr
var

exit
root@weiba-PC:/home/weiba/mynginx# docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
0a4f6cf3ef61        ubuntu              "/bin/bash"         54 seconds ago      Up 53 seconds                           fervent_minsky
root@weiba-PC:/home/weiba/mynginx# docker exec -i -t 0a4f bash
root@0a4f6cf3ef61:/# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
```
**如果从这个 stdin 中 exit，不会导致容器的停止。推荐大家使用 docker exec**

## 安装 Nexus3 私有仓库
```
docker run -d --name nexus3 --restart=always \
    -p 8081:8081 \
    --mount src=nexus-data,target=/nexus-data \
    sonatype/nexus3
```
等待 3-5 分钟，如果 nexus3 容器没有异常退出，那么你可以使用浏览器打开 http://YourIP:8081 访问 Nexus 了。

第一次启动 Nexus 的默认帐号是 admin 密码是 admin123 登录以后点击页面上方的齿轮按钮进行设置。

第一次启动 Nexus 的默认帐号是 admin 密码是 admin123 登录以后点击页面上方的齿轮按钮进行设置。