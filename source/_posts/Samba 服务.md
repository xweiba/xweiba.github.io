---
title: Samba 服务
date: 2022-04-03 13:06:49
tags:
 - Samba
categories:
 - Samba
---

## 问题记录
### 无法直接打开 `exe` 文件
在 `/etc/samba/smb.conf` 的 `global` 节点中添加 `acl allow execute always = Yes`后重启服务即可。

