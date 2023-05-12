---
title: Redis Cluster 修复
date: 2023-5-13 0:32:15
tags:
 - Redis
categories:
 - Redis
---

# Redis Cluster 修复

## 节点fair，没有可用槽位

```bash
redis-cli -a xxx -c -h 192.168.186.xx -p xxx
# 查看节点状态
cluster info
# 查看节点信息
cluster nodes
# 手动添加节点
cluster meet 192.168.186.xx xxxx
# 修复槽位
redis-cli -a xxx --cluster fix 192.168.186.xx:xxxx
```

