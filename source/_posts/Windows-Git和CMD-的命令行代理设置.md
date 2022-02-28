---
title: Windows Git 和 CMD 终端代理设置
date: 2022-02-28 10:10:42
tags:
 - Git
 - cmd
 - windows 
 - proxy
 - SOCKS
categories:
 - 代理
---

> 国内现在访问 `git` 速度太慢了，记录下配置的代理，来自 [Windows git和cmd代理设置](https://www.jianshu.com/p/b9047a59ffc9)

## 配置代理

代理类型是 `http` 值为: `http://127.0.0.1:7777`, 为 `SOCKS5` 时值为: `socks5://127.0.0.1:7777`

### Git 代理

临时代理:

```bash
export http_proxy=http://127.0.0.1:7777
export https_proxy=http://127.0.0.1:7777
```

永久代理:

- 命令方式：

  ```bash
  git config --global http.proxy http://127.0.0.1:7777
  git config --global https.proxy http://127.0.0.1:7777
  ```

- 修改配置文件方式, 进入用户名根路径，找到 `.gitconfig` 文件，修改为：

  ```bash
  [http]
  proxy = http://127.0.0.1:7777
  [https]
  proxy = http://127.0.0.1:7777
  ```

查看代理状态:

```bash
git config --get --global http.proxy
git config --get --global https.proxy
```

单独查看 `socks5` 代理模式情况:

```bash
git config --get --global http.proxy socks5
git config --get --global https.proxy socks5
```

取消代理:

```bash
git config --global --unset http.proxy
git config --global --unset https.proxy
```

### CMD 代理

临时代理：

```bash
set http_proxy=http://127.0.0.1:7777
set https_proxy=http://127.0.0.1:7777
```

永久代理：

```yaml
netsh winhttp import proxy source=ie
```

针对性代理，绕过本地请求：

```
netsh winhttp set proxy proxy-server="http=192.168.17.100:50015" bypass-list="localhost"
```

查看代理状态：

```bash
netsh winhttp show proxy
```

取消代理：

```bash
netsh winhttp reset proxy
```

