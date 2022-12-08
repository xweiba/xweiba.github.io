---
title: Git 常用命令
date: 2020-02-08 11:35:17
tags:
 - Git
categories:
 - Git
---

# 常用操作

- `git add .` 添加更新文件
- `git push` 提交文件
- `git pull` 拉取文件
- `git commit -m "提交说明"` 提交文件



# Git 版本回退

1. `git log` 查看提交 `commit id`, 按 `q` 退出
2. `git reset --hard commitId`  回退版本
3. `git push -f` 强制推送

注意此操作会强制覆盖远程仓库.
