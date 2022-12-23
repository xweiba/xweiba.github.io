---
title: DDNS和证书配置
date: 2022-12-23 14:42:26
tags:
 - DDNS
 - HTTPS
categories:
 - DDNS
---

# DDNS
> 项目地址：https://github.com/rehiy/dnspod-shell

### 一、编辑配置文件
`vim ddnspod.sh`
```vim
. /root/dnspod-shell/ardnspod

arToken="xx,xx"

# IPv4:
arDdnsCheck "xx.cf" "@"
arDdnsCheck "xx.cf" "*"：
arDdnsCheck "xx.ml" "@"
arDdnsCheck "xx.ml" "*"
```
### 二 添加定时任务，每小时1次
`crontab -e`
```
0 * * * * /root/dnspod-shell/ddnspod.sh > /root/dnspod-shell/ddnspod.log
```

# acme
> 项目地址：https://github.com/acmesh-official/acme.sh

### 一、安装并重载环境变量
```bash
curl https://get.acme.sh | sh -s email=xxx@qq.com
source ~/.bashrc
# 自动更新
acme.sh --upgrade --auto-upgrade
```
### 二、设置DNS KEY
```bash
export DP_Id="xxx"
export DP_Key="xxx"
```

### 三、生成域名与泛域名证书
```bash
acme.sh --issue --dns dns_dp -d xx.cf -d *.xx.cf
acme.sh --issue --dns dns_dp -d xx.ml -d *.xx.ml
```

## 合成带 `fullchain` 的cer文件
```bash
cat xxx.cf.cer fullchain.cer >> xxx.cf.pem
```