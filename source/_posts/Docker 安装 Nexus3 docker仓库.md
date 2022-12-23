---
title: Docker 安装 Nexus3 docker仓库
date: 2019-09-10 11:30:54
tags:
 - docker
 - nexus3
categories:
 - Docker
---

使用容器安装`Nexus3` 仓库，真的非常方便

# 下载镜像
nexus3 的镜像名称为 sonatype/nexus3 我们直接用 docker 工具下载即可：
```
docker pull sonatype/nexus3
```

# 运行镜像
注意这里需要开启后面docker仓库的端口，不然连不上，不知道为啥教程都没说
```
docker run -d -p 8081:8081 -p 8082:8082 --name nexus3 sonatype/nexus3
```

可以使用logs看日志
```
docker logs -f nexus3
```

显示到如图所示，即运行成功， 浏览器打开 [http://localhost:8081](http://localhost:8081)
![title](leanote://file/getImage?fileId=5c18f2b875a1b808b2000093)

默认账号/密码： `admin/admin123`

# 创建 docker 仓库
登录后在`repositories - Create repository` 创建仓库，类型选择`docker(hosted)`, 名字随便取，主要是这个api端口一定要与映射的一致。

![title](leanote://file/getImage?fileId=5c18f2b875a1b808b200008f)

## 注意
**如不勾选兼容v1 API 将无法支持`docker search 127.0.0.1:8082/weiba` 搜索**

## **v2 API** 
- `curl 127.0.0.1:8082/v2/_catalog` - 查看仓库镜像列表
- `curl 127.0.0.1:8082/v2/ubuntu/tags/list` - 查看镜像tag列表

# 创建安全规则
在 `security - Roles - Create role` ， 在`privlleges`上输入`docker`全选到右侧
![title](leanote://file/getImage?fileId=5c18f2b875a1b808b2000096)

# 创建账号
在 `security - Users Create user` ， 在`available`中选择`docker`

# docker 登录仓库
使用下面的命令登录：
```
docker login 127.0.0.1:8082
```

登录一次后会保存密码，下次不用再输入。

退出登录
```
docker logout
```

# 推送镜像到仓库

首先给镜像打标签
```
sudo docker tag ubuntu:latest 127.0.0.1:8082/ubuntu:weiba   
```
看看是否存在：
![title](leanote://file/getImage?fileId=5c18f2b875a1b808b2000092)

推送镜像：
```
sudo docker push 127.0.0.1:8082/ubuntu
```
![title](leanote://file/getImage?fileId=5c18f2b875a1b808b200008e)

删除本地镜像，再从本地仓库获取测试：
```
weiba@weiba-PC:~$ sudo docker rmi -f 127.0.0.1:8082/ubuntu:weiba
Untagged: 127.0.0.1:8082/ubuntu:weiba
Untagged: 127.0.0.1:8082/ubuntu@sha256:acd85db6e4b18aafa7fcde5480872909bd8e6d5fbd4e5e790ecc09acc06a8b78
weiba@weiba-PC:~$ sudo docker rmi -f ubuntu:latest
Untagged: ubuntu:latest
Untagged: ubuntu@sha256:6d0e0c26489e33f5a6f0020edface2727db9489744ecc9b4f50c7fa671f23c49
Deleted: sha256:93fd78260bd1495afb484371928661f63e64be306b7ac48e2d13ce9422dfee26
Deleted: sha256:1c8cd755b52d6656df927bc8716ee0905853fada7ca200e4e6954bd010e792bb
Deleted: sha256:9203aabb0b583c3cf927d2caf6ba5b11124b0a23f8d19afadb7b071049c3cf26
Deleted: sha256:32f84095aed5a2e947b12a3813f019fc69f159cb5c7eae5dad69b2d98ffbeca4
Deleted: sha256:bc7f4b25d0ae3524466891c41cefc7c6833c533e00ba80f8063c68da9a8b65fe
```
![title](leanote://file/getImage?fileId=5c18f2b875a1b808b2000091)

从本地仓库下载镜像：
```
weiba@weiba-PC:~$ sudo docker pull 127.0.0.1:8082/ubuntu:weiba
weiba: Pulling from ubuntu
32802c0cfa4d: Pull complete 
da1315cffa03: Pull complete 
fa83472a3562: Pull complete 
f85999a86bef: Pull complete 
Digest: sha256:acd85db6e4b18aafa7fcde5480872909bd8e6d5fbd4e5e790ecc09acc06a8b78
Status: Downloaded newer image for 127.0.0.1:8082/ubuntu:weiba
```
查看镜像列表：
![title](leanote://file/getImage?fileId=5c18f2b875a1b808b2000094)

web端查看：
![title](leanote://file/getImage?fileId=5c18f2b875a1b808b2000095)


扩展资料：
- [更改仓库镜像地址为阿里云](https://www.jianshu.com/p/fdb6f77401b6)
- [持久化数据，优化配置](https://yeaheo.com/post/docker-nexus3-installation/)


# 注意事项
如果你不想使用 127.0.0.1:5000 作为仓库地址，比如想让本网段的其他主机也能把镜像推送到私有仓库。你就得把例如 192.168.199.100:5000 这样的内网地址作为私有仓库地址，这时你会发现无法成功推送镜像。

这是因为 Docker 默认不允许非 HTTPS 方式推送镜像。我们可以通过 Docker 的配置选项来取消这个限制，或者查看下一节配置能够通过 HTTPS 访问的私有仓库。

## Ubuntu 14.04, Debian 7 Wheezy
对于使用 upstart 的系统而言，编辑 `/etc/default/docker` 文件，在其中的 DOCKER_OPTS 中增加如下内容：
```
DOCKER_OPTS="--registry-mirror=https://registry.docker-cn.com --insecure-registries=192.168.199.100:5000"
```
重新启动服务。
```
sudo service docker restart
```

## Ubuntu 16.04+, Debian 8+, centos 7
对于使用 systemd 的系统，请在 `/etc/docker/daemon.json` 中写入如下内容（如果文件不存在请新建该文件）
```
{
  "registry-mirror": [
    "https://registry.docker-cn.com"
  ],
  "insecure-registries": [
    "192.168.199.100:5000"
  ]
}
```
**注意：该文件必须符合 json 规范，否则 Docker 将不能启动。**


## NGINX 加密代理
证书的生成请参见 私有仓库高级配置 里面证书生成一节。

NGINX 示例配置如下
```
upstream register
{
    server "YourHostName OR IP":5001; #端口为上面添加的私有镜像仓库是设置的 HTTP 选项的端口号
    check interval=3000 rise=2 fall=10 timeout=1000 type=http;
    check_http_send "HEAD / HTTP/1.0\r\n\r\n";
    check_http_expect_alive http_4xx;
}

server {
    server_name YourDomainName;#如果没有 DNS 服务器做解析，请删除此选项使用本机 IP 地址访问
    listen       443 ssl;

    ssl_certificate key/example.crt;
    ssl_certificate_key key/example.key;

    ssl_session_timeout  5m;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers  HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers   on;
    large_client_header_buffers 4 32k;
    client_max_body_size 300m;
    client_body_buffer_size 512k;
    proxy_connect_timeout 600;
    proxy_read_timeout   600;
    proxy_send_timeout   600;
    proxy_buffer_size    128k;
    proxy_buffers       4 64k;
    proxy_busy_buffers_size 128k;
    proxy_temp_file_write_size 512k;

    location / {
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header X-Forwarded-Port $server_port;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection $connection_upgrade;
        proxy_redirect off;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_pass http://register;
        proxy_read_timeout 900s;

    }
    error_page   500 502 503 504  /50x.html;
}
```

## Docker 主机访问镜像仓库
如果不启用 SSL 加密可以通过前面章节的方法添加信任地址到 Docker 的配置文件中然后重启 Docker

使用 SSL 加密以后程序需要访问就不能采用修改配置的访问了。具体方法如下：
```
$ openssl s_client -showcerts -connect YourDomainName OR HostIP:443 </dev/null 2>/dev/null|openssl x509 -outform PEM >ca.crt
$ cat ca.crt | sudo tee -a /etc/ssl/certs/ca-certificates.crt
$ systemctl restart docker
```

使用 `docker login YourDomainName OR HostIP` 进行测试，用户名密码填写上面 Nexus 中生成的。