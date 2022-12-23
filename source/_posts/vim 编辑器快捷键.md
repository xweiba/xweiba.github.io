---
title: vim 编辑器快捷键
date: 2021-04-26 13:08:44
tags:
 - Vim
 - 常用
categories:
 - Vim
---

vim可以直接编辑jar等压缩包内容: vim xx.jar 再选择压缩包内配置文件修改.

# 常用命令
- 文本替换: 命令模式 `:` 下: `%s/prepare-lesson-microservice/testiiiii/g`
- 删除所有内容: `ggdG`
- 显示/不显示行号: 命令模式 `:` 下: `set nu/nonu`
- 无权限时保存文件: `:w !sudo tee %`