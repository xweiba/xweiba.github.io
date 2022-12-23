---
title: Docker 安装记录
date: 2022-12-23 14:48:39
tags:
 - Docker
 - 安装记录
categories:
 - Docker
---

# 可道云

> 使用内置nginx，代理相关服务

```bash
docker run -d \
--name cloudfile \
--dns 192.168.186.210  \
-p 12880:12880 \
-e PUID=1000 -e PGID=1000 \
-v /mnt:/mnt \
-v /root/.acme.sh:/root/.acme.sh \
-v /mnt/nas/application/nginx/etc/nginx/sites-enabled:/etc/nginx/sites-enabled \
-v /mnt/nas/application/nginx/etc/nginx/password:/etc/nginx/password \
-v /mnt/nas/application/kodcloud/kodbox/data:/var/www/html \
--restart always kodcloud/kodbox:latest
```

# nginxwebui 

> ng配置编辑服务

```bash
docker run -itd  \
--name ngweb \
-e BOOT_OPTIONS="--server.port=12881" \
-p 12881:12881 \
-e PUID=1000 -e PGID=1000 \
-v /mnt/nas/application/nginx/etc/nginx/:/etc/nginx/ \
-v /root/.acme.sh:/root/.acme.sh \
--restart always cym1102/nginxwebui:latest
```
### 配置
1. http请求跳转https
```nginx
server {
    ...
    error_page 497 = https://$host:12880$request_uri;
    ...
}
```
2. 配置文件修改后执行：
`cat reNginx.sh`
```bash
#!/bin/bash

rm -rf /mnt/nas/application/nginx/etc/nginx/sites-enabled/* && cp /mnt/nas/application/nginx/etc/nginx/conf.d/* /mnt/nas/application/nginx/etc/nginx/sites-enabled/ && docker container restart cloudfile
```

# mariadb-mysql

```bash
docker run -d \
--name mysql \
-p 3306:3306 \
-e PUID=1000 -e PGID=1000 \
-e MYSQL_ROOT_PASSWORD=password \
--user 1000:1000 \
-v /mnt/nas/application/mariadb/data:/var/lib/mysql \
--restart always mariadb:latest
```

# Redis

```bash
docker run -itd \
--name redis \
-p 6379:6379 \
-v /mnt/nas/application/redis/data:/data \
-e PUID=1000 -e PGID=1000 \
--user 1000:1000 \
--restart always redis:latest
```

# nyanmisaka/jellyfin
> jellyfin 中国优化版

```bash
docker run -d \
--name jf \
-e PUID=$(id -u) -e PGID=$(id -g) \
-e TZ=Asia/Shanghai \
-p 8099:8096 \
-e HTTP_PROXY=http://192.168.186.210:7890 \
-e HTTPS_PROXY=http://192.168.186.210:7890 \
-v /mnt/nas/application/jellyfin:/config \
-v /mnt/nas/data:/data \
-v /mnt:/mnt \
--device /dev/dri:/dev/dri \
--restart always nyanmisaka/jellyfin:latest
```

宿主机安装：
```
apt install intel-gpu-tools
apt-get -y install vainfo
```

**更改硬解配置后必须重启**

# 迅雷群晖版
> 必须用root权限运行, 非会员每天只能下载几个资源

```bash
docker run -d \
--name=xunlei \
--hostname=MyNas \
--net=host \
-e XL_WEB_PORT=2345 \
-v /mnt/nas/application/xunlei/data:/xunlei/data \
-v /mnt/nas/data:/xunlei/downloads \
--restart always --privileged cnk3x/xunlei:latest
```

# aria2
```bash
docker run -d \
    --name aria2 \
    --restart always  \
    --log-opt max-size=1m \
    -e PUID=1000 \
    -e PGID=1000 \
    -e UMASK_SET=022 \
    -e RPC_SECRET=656CjCKQ0iQUB78eXgd2VpYmExMDI4 \
    -e RPC_PORT=6800 \
    -p 6800:6800 \
    -e LISTEN_PORT=6888 \
    -p 6888:6888 \
    -p 6888:6888/udp \
    -v /mnt/nas/application/aria2/config:/config \
    -v /mnt/nas/data/downloads:/downloads \
    -v /mnt:/mnt \
    p3terx/aria2-pro
```
# qBittorrent 
> Docker qBittorrent 中国优化版: [https://github.com/SuperNG6/docker-qbittorrent](https://github.com/SuperNG6/Docker-qBittorrent-Enhanced-Edition)
> 默认用户名:admin;默认密码:adminadmin

```bash
docker run -d  \
    --name=qb  \
    -e WEBUIPORT=8080  \
    -e PUID=1000 \
    -e PGID=1000 \
    -e TZ=Asia/Shanghai \
    -p 16881:16881  \
    -p 16881:16881/udp  \
    -p 16080:8080  \
    -v /mnt/nas/application/qBittorrent:/config  \
    -v /mnt/nas/data/downloads:/downloads  \
    -v /mnt:/mnt \
    --restart always  \
    superng6/qbittorrent:latest
```
启动有点慢。

### 问题记录
1. [qBittorrent添加Trackers后显示未联系-docker已修复](https://sleele.com/2019/05/25/qbittorrent%E6%B7%BB%E5%8A%A0trackers%E5%90%8E%E6%98%BE%E7%A4%BA%E6%9C%AA%E8%81%94%E7%B3%BB/)
在qBittorrent.conf文件中的Preferences下添加如下内容，重启qBittorrent即可
```
Advanced\AnnounceToAllTrackers=true
```

# transmissionic
> 太耗CPU了，直接用qb吧

```bash
docker run -d \
  --name tran \
  -e PUID=1000 \
  -e PGID=1000\
  -e TZ=Asia/Shanghai \
  -e TRANSMISSION_WEB_HOME=/transmissionic/ `#optional` \
  -e USER=userName \
  -e PASS=password  \
  -p 9091:9091 \
  -p 51413:51413 \
  -p 51413:51413/udp \
  -v /mnt:/mnt \
  -v /mnt/nas/application/transmission/config:/config \
  -v /mnt/nas/data/downloads:/downloads \
  -v /mnt/nas/application/transmission/watch:/watch \
  --restart always \
  lscr.io/linuxserver/transmission:latest
```

# photoprism
耗资源，不用了
```
docker run -d \
  --name photo \
  -e PUID=1000 \
  -e PGID=1000 \
-p 2342:2342 \
-v /mnt/nas/data/相册:/photoprism/originals \
-v /mnt/nas/data/backups/userName/相册:/photoprism/originals/userName
-v /mnt/nas/application/photoprism/storage:/photoprism/storage \
-v /mnt/nas/application/photoprism/import:/photoprism/import \
-v /mnt:/mnt \
-e PHOTOPRISM_DATABASE_DRIVER=mysql \
-e PHOTOPRISM_ADMIN_PASSWORD=password \
-e PHOTOPRISM_DATABASE_SERVER=192.168.186.230:3306 \
-e PHOTOPRISM_DATABASE_NAME=photoprism \
-e PHOTOPRISM_DATABASE_USER=root \
-e PHOTOPRISM_DATABASE_PASSWORD=password \
-e Allow_uploads_that_MAY_be_offensive=true \
--device /dev/dri:/dev/dri \
--restart always thielepaul/photoprism:db-api
```


# clouddrive
先执行  mount --make-shared /mnt/nas, 添加到开机脚本，初始化时应用启动后再设置对应网盘的挂载点，把/mnt放进去，
要在docker加载之前执行，我就写在 vim /etc/init.d/docker 开头了

```bash
docker run -d \
--name clouddrive \
-p 9798:9798 \
--privileged \
--device /dev/fuse:/dev/fuse \
-v /mnt/nas/application/clouddrive:/Config \
-v /mnt/nas:/media:shared \
--restart always \
cloudnas/clouddrive:latest
```

# piwigo
> 难用, 照片多了以后无法导入

```bash
docker run -d \
  --name=photo \
  --dns 192.168.186.210 \
  -e PUID=1000 \
  -e PGID=1000 \
  -e TZ=Asia/Shanghai \
--env HTTP_PROXY="http://192.168.186.210:7890" \
--env HTTPS_PROXY="http://192.168.186.210:7890" \
--env http_proxy="http://192.168.186.210:7890" \
--env https_proxy="http://192.168.186.210:7890" \
  -p 2342:80 \
  -v /mnt/nas/application/piwigo/config:/config \
  -v /mnt/nas/application/piwigo/gallery:/gallery \
  -v /mnt/nas/data/相册:/gallery/galleries/userName \
  -v /mnt/nas/data/backups/userName/相册/:/gallery/galleries/userName/backups \
  -v /mnt/nas/application/kodcloud/kodbox/data/data/files:/gallery/galleries/可道云 \
  --restart always \
  lscr.io/linuxserver/piwigo:latest
```

配置支持中文
```
unless-stopped
$conf['sync_chars_regex'] = '/^[\x{0800}-\x{9fa5}a-zA-Z0-9-_.\(\)\[\]\ 【】（）, ·^×{}=★☆@#\s+]+$/u';*/
```

# OnlyOffice

```bash
docker run -itd \
--name onlyoffice \
--dns 192.168.186.210 \
-e PUID=1000 \
-e PGID=1000 \
--restart always \
-p 8081:80 \
-v /app/onlyoffice/DocumentServer/logs:/mnt/nas/application/onlyoffice/logs \
-v /app/onlyoffice/DocumentServer/data:/mnt/nas/application/onlyoffice/data \
onlyoffice/documentserver
```

# drawio
```bash
docker run -itd \
--name="draw-io" \
--restart=always \
--dns 192.168.186.210 \
-e PUID=1000 \
-e PGID=1000 \
-p 8082:8080 \
-v /mnt:/mnt \
fjudith/draw.io
```

# Hexo

```
docker run  -itd \
--name="blog" \
--env HTTP_PROXY="http://192.168.186.210:7890" \
--env HTTPS_PROXY="http://192.168.186.210:7890" \
--env http_proxy="http://192.168.186.210:7890" \
--env https_proxy="http://192.168.186.210:7890" \
--env PUBLIC_HEXO_GITHUB_URL=https://github.com/xweiba/xweiba.github.io.git \
-p 16990:4000 \
-v /mnt/nas/application/hexo/build_and_run.sh:/build_and_run.sh \
--restart always \
zeusro/hexo:latest  
```

build_and_run.sh:

```shell
dir_git=${PUBLIC_HEXO_GITHUB_URL##*/}
#rm -rf ${dir_git%.*}
git clone $PUBLIC_HEXO_GITHUB_URL
cd ${dir_git%.*}
git checkout source
npm install
hexo generate
```

```
docker run -it \
--name hexo \
-e PUID=1000 \
-e PGID=1000 \
-v /mnt/nas/data/资料/code/GitHub/xweiba/xweiba.github.io:/home/hexo/.hexo \
-p 16990:4000 \
taskbjorn/hexo
--restart always \
```

