---
title: Puppeteer 配置记录
date: 2022-12-23 15:02:52
tags:
 - Puppeteer
 - 配置
categories:
 - Puppeteer
---


# Puppeteer项目配置

**注意事项：**

- CentOS 7 以下不支持 Puppeteer
- Puppeteer 服务需能够访问被打印的页面
- 请先确保字体安装成功，否则会导致PDF渲染异常

## 字体安装

安装 `fontconfig mkfontscale` ：

```
yum -y install fontconfig mkfontscale
```

将源码目录fonts文件夹下字体文件夹复制至服务器的 `/usr/share/fonts` 目录下, 然后执行以下命令重建字体缓存，并查看字体是否已安装，出现 `/usr/share/fonts/puppeteer` 目录即为正常。

```
mkfontscale
mkfontdir
fc-cache
fc-list :lang=zh
```

## 依赖安装

安装 `chrome puppeteer` 所需依赖：

```shell
yum -y install atk at-spi2-atk libxkbcommon-x11-devel libXcomposite gtk3 alsa-lib-devel
```

## 部署源码文件

```shell
# 上传源码
unzip puppeteer-microservice.zip
npm install 
npm run start # 启动测试
```
## Windows 与 Linux 渲染结果不一致
Github issues: [2410](https://github.com/puppeteer/puppeteer/issues/2410)
1. 请确认渲染字体一致。
2. 请确认Puppeteer服务端配置中包含以下配置：
  ```
  puppeteer args:
  '--disable-font-subpixel-positioning', // 必须添加，解决空格问题
  '--font-render-hinting=none' // 可选添加，当前业务加不加效果都一样
  
  css: // 可选添加，当前业务加不加效果都一样
  * {
        text-rendering: geometricprecision !important;
    }
  ```

## 配置文件说明：
[args 参数说明文档](https://peter.sh/experiments/chromium-command-line-switches/)
```js
module.exports = {
    port: 9572,
    puppeteer: {
        headless: true, // 是否启用无头模式页面
        //executablePath: '/usr/lib64/chromium-browser/headless_shell',
        args: [
            '–-disable-gpu',
            '-–disable-dev-shm-usage',
            '-–disable-setuid-sandbox',
            '-–no-first-run',
            '--no-sandbox',
            '-–no-zygote',
            '-–single-process',
         '--disable-font-subpixel-positioning', // 必须添加，解决windows和linux下渲染结果不一致问题
         '--font-render-hinting=none'// 必须添加，解决windows和linux下渲染结果不一致问题
        ],
        ignoreHTTPSErrors: true,
        timeout: 0
    },
    browserPool: {
        min: 1, // 池中最小实例数
        max: 5, // 池中最大实例数
        idleTimeoutMillis: 1000 * 60 * 60 * 24, // 实例存活时间，超时将重新创建新的实例，防止内存泄漏，浏览器实例默认24小时
        evictionRunIntervalMillis: 1000 * 60 * 60 // 实例存活检测时间
    },
    pagePool: {
        min: 5,
        max: 10,
        cacheDisabled: true, // 是否禁用页面缓存
        idleTimeoutMillis: 1000 * 60 * 60, // 页面实例默认一小时重新创建一次
        evictionRunIntervalMillis: 1000 * 60
    },
    tempRootDir: '/home/icampus3.0/pkgs/puppeteer-microservice/temp',
    contextPath: '/microservice/puppeteerservice'
}
```

## 服务脚本

stop.sh

```shell
#!/bin/bash
#######################
# created by code on 2021/10/25
#######################
function stop(){
       serviceName=puppeteer-microservice
       local pid=$(ps aux|grep "puppeteer-microservice"|grep -v grep|awk '{print $2}' 2>>/dev/null)

       if [[ -n $pid ]]; then

            kill -9 $pid 2>>/dev/null

            if [[ $? -ne 0 ]]; then
                 echo -e "  Failed to stop service of ${serviceNam}"
                 return 1
            else
                 echo -e "  Successful to stop service of ${serviceNam}"
                 return 0
            fi
       fi

}
stop

```

start.sh

```shell
#!/bin/bash
#######################
# created by code on 2021/10/25
#######################
function setVars(){
       serviceName=puppeteer-microservice
       basepath=/home/icampus3.0
       jdkHome=${basepath}/jdk8
       servHome=${basepath}/pkgs/${serviceName}
       start=${servHome}/start.sh
       log=${basepath}/logs/${serviceName}.log
       npm=/usr/local/n/versions/node/14.18.1/bin/npm
       option="run start"
}
function start(){
       cd $servHome
       sh stop.sh
       echo -e "  Start to start up ${serviceName} : "

       echo -e "${npm} ${option}"
       ${npm} ${option} > $log 2>&1 &

       echo $! > pid

       if [[ -z $(cat pid) ]]; then
           echo -e "  Failed to start up ${serviceName}"

           return 1
       else
           echo -e "  Successful to start up ${serviceName}"

           return 0
       fi
}

setVars
start
```

## 可选安装

~~**chrome不同内核版本，css有兼容性问题，一律使用最新版本。**~~  兼容性问题已修复

网上说这个版本的Chrome性能比较好：[无头浏览器性能对比与Puppeteer的优化文档](https://blog.it2048.cn/article-headless-puppeteer/), 实际测试下来差不多。

```
yum install chromium-headless
/usr/lib64/chromium-browser/headless_shell 调用路径
```

安装依赖时执行下面的命令跳过Chrome的下载

```
npm install puppeteer --ignore-scripts
```

## Nginx配置

```nginx
upstream puppeteer-microservice {
   server xxxx:xxxx;
}

location ^~ /microservice/puppeteerservice {
    proxy_set_header Host $http_host;
    proxy_redirect off;
    proxy_pass http://puppeteer-microservice/microservice/puppeteerservice;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    client_max_body_size 1000m;
}
```

# Puppeteer服务接口

## Web转PDF服务

[GET] `/microservice/puppeteerservice/pdf`  

参数：

| params      | Description |
| ----------- | ----------- |
| htmlUrl      | base64后的待生成PDF的页面地址|

htmlUrl参数：

| htmlUrl      | Description |
| ----------- | ----------- |
| format      | A3 或 A4, 默认 A4|
| printBackground | 是否打印背景图, 默认false |
