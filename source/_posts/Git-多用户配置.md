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

编辑 `~/.ssh` 目录下的 `config` 文件, `vim ~/.ssh/config`

```bash
Host github.com
    HostName github.com
    User xxxx
    IdentityFile ~/.ssh/id_rsa_github
    HostkeyAlgorithms +ssh-rsa
    PubkeyAcceptedKeyTypes +ssh-rsa
Host gitlab-xxx.com
    HostName gitlab-xxx.com
    User xxxxxx
    IdentityFile ~/.ssh/id_rsa_gitlab
    HostkeyAlgorithms +ssh-rsa
    PubkeyAcceptedKeyTypes +ssh-rsa
```

测试：

```bash
ssh -T git@github.com
Hi xxxx! You've successfully authenticated, but GitHub does not provide shell access.
ssh -T git@gitlab-xxx.com
Welcome to GitLab, xxxxxx!
```

#### 4.仓库配置

完成以上配置后，其实你已经基本完成了所有配置。分别进入附属于 `GitHub` 和 `GitLab` 的仓库，此时都可以进行 `git` 操作了。但是别急，如果你此时提交仓库修改后，你会发现提交的用户名变成了你的系统主机名。

这是因为 git 的配置分为三级别，`System —> Global —>Local`。`System` 即系统级别，`Global` 为配置的全局，`Local` 为仓库级别，优先级是 `Local > Global > System`。

因为我们并没有给仓库配置用户名，又在一开始清除了全局的用户名，因此此时你提交的话，就会使用 `System` 级别的用户名，也就是你的系统主机名了。

因此我们需要为每个仓库单独配置用户名信息，假设我们要配置 `Github` 的某个仓库，进入该仓库后，执行：

```
git config --local user.name "xxx"
git config --local user.email "xx@gmail.com"
```

执行完毕后，通过以下命令查看本仓库的所有配置信息：

`git config --local --list`

至此你已经配置好了 `Local` 级别的配置了，此时提交该仓库的代码，提交用户名就是你设置的 `Local` 级别的用户名了。

### 5.添加 `Git-Bash` 启动脚本

由于 `ssh-agent` 是存放在高速缓存中的，重启后添加的 `ssh-add` 就会失效，这里直接在 `git-bash` 的 `bash_profile` 中添加：

```bash
# windows默认会生成该文件
test -f ~/.profile && . ~/.profile
test -f ~/.bashrc && . ~/.bashrc
```

在 `.bashrc` 中添加：

```shell
eval `ssh-agent` > /dev/null
ssh-add ~/.ssh/id_rsa_github > /dev/null
ssh-add ~/.ssh/id_rsa_xuechuang > /dev/null
```

每次启动 `git-bash` 都会自动启动并添加。

test
