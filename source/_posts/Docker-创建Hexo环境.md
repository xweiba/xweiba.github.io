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

# 声明项目所用端口
EXPOSE 4000

# 这个脚本后面可以自己替换
COPY start.sh /usr/local/start.sh
RUN chmod +x /usr/local/start.sh
CMD ["/bin/bash", "-c", "./start.sh"]

```

start.sh

```shell
#!/bin/bash
if [ ! -d "/usr/local/xweiba.github.io" ]; then
  git clone -b source https://github.com/xweiba/xweiba.github.io.git&&npm config set registry https://registry.npmmirror.com&&npm install hexo-cli -g
fi
cd /usr/local/xweiba.github.io
git pull&&npm install&&hexo clean&&hexo s
```

