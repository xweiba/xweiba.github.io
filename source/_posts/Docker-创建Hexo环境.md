---
title: Docker-创建Hexo环境
date: 2022-12-23 23:14:30
tags:
 - Docker
 - Hexo
 - nodejs
categories:
 - Docker
---

> GitHub 访问速度不佳, 在Nas上部署一套Hexo

# 编写DockerFile

本来是打算看看有没有合适的镜像的, 不是太大, 就是配置太麻烦, 干脆自己自定义一个算了

```dockerfile
FROM node:16.0.0-alpine

# 设置代理, 后边装依赖要用
ENV http_proxy http://192.168.186.210:7890
ENV https_proxy http://192.168.186.210:7890

WORKDIR /usr/local

# alpine中什么都没有, 需要单独安装
RUN apk update && apk add bash && apk add git
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

RUN unset http_proxy && unset https_proxy

# hexo端口
EXPOSE 4000
# 浏览器自动刷新端口
EXPOSE 3000

# 这个脚本后面可以自己替换
COPY start.sh /usr/local/start.sh
RUN chmod +x /usr/local/start.sh
CMD ["/bin/bash", "-c", "./start.sh"]

```

start.sh

```shell
#!/bin/bash
if [ ! -d "/usr/local/xweiba.github.io" ]; then
  git clone -b source https://github.com/xweiba/xweiba.github.io.git&&npm config set registry https://registry.npmmirror.com&&npm install hexo-cli -g && npm install -g browser-sync
fi
cd /usr/local/xweiba.github.io
git pull --force&&npm install&&hexo clean&&hexo s
```

# 构建镜像并启动

构建镜像:

```bash
docker build -t wb-hexo:v1 .
```

启动, 第一次会比较慢, 后面就好了

```bash
docker run -itd --name hexo -p 4000:4000 -p 4000:4000 --restart always wb-hexo:v1
docker container logs -f hexo
```
# 添加Nginx配置

```nginx
server {
  server_name blog.weiba.ml;
  listen 32880 ssl http2;
  ssl_certificate /root/.acme.sh/weiba.ml/weiba.ml.pem;
  ssl_certificate_key /root/.acme.sh/weiba.ml/weiba.ml.key;
  ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
  error_page 497 = https://$host:32880$request_uri;
  location / {
    proxy_pass http://192.168.1.1:4000;
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Host $http_host;
    proxy_set_header X-Forwarded-Port $server_port;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection "upgrade";
  }
}
```

# 定时拉取更新

alpine 镜像的 cron 功能应该是废的, 直接在宿主机上执行, 半小时执行一次.

```
# crontab -e
*/30 * * * * docker exec -it hexo bash -c "cd /usr/local/xweiba.github.io&&git pull --force"
```

# Hexo 添加自动刷新插件

## 1. 安装 Browsersync

在任意目录下执行

```bash
npm install -g browser-sync
```

安装完成后利用 `browser-sync --version` 来检测是否安装成功

## 2. 安装 Hexo 插件

在 Hexo 目录下执行

```bash
npm install hexo-browsersync --save
```

开启https支持

```
browsersync:
  https: true
```

