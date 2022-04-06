---
title: Git 多用户配置
date: 2022-04-06 11:01:39
tags:
  - Git
 
---

## Git 多用户配置

> 公司部署了私有 `GitLab`, 在公司更新个人的 `GitHub` 项目的时候非常麻烦，好在 `Git` 是支持多用户的，记录一下。
>
> 参考：
>
> - [Git 多用户配置 - 苍青浪](https://www.cnblogs.com/cangqinglang/p/12462272.html)
> - [在git-bash启动时启用SSH Agent | 工程师](https://www.angtylook.com/2021/07/11/ssh-agnt.html)

### 1. 配置各环境秘钥对

钥对的保存位置默认在 ~/.ssh 目录下，使用一下命令生成

```bash
ssh-keygen -t rsa -C “xxxx@gmail.com” # github邮箱
# 按下 ENTER 键后，会有如下提示：
# Generatingpublic/privatersa key pair.Enter fileinwhich to save the key (/Users/xxx/.ssh/id_rsa):
# 为了和 GitLab 区分，这里重命名为 id_rsa_github
ssh-keygen -t rsa -C “xxxx@xxx.com” # 公司邮箱
# 重命名为 id_rsa_gitlab
```

将生成的 `_pub` 后缀的公钥文件内容复制至 `GitHub` 和 `GitLab` 的 ` SSH Keys` 中。

### 2. 添加到 `SSH-Agent` 中

在上一步中，我们已经将公钥添加到了 `GitHub`  或者 `GitLab`  服务器上，我们还需要将私钥添加到本地中，不然无法使用。

在终端执行: 

```bash
eval 'ssh-agent'
ssh-add ~/.ssh/id_rsa_github
ssh-add ~/.ssh/id_rsa_gitlab
```

### 3. 管理秘钥

通过以上步骤，公钥、密钥分别被添加到 git 服务器和本地了。下面我们需要在本地创建一个密钥配置文件，通过该文件，实现根据仓库的 `remote` 链接地址自动选择合适的私钥。

编辑 `~/.ssh` 目录下的 `config` 文件

```bash
vim ~/.ssh/config
```



