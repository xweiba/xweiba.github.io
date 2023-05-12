---
title: RocketMQ找不到Broker节点
date: 2023-5-13 0:32:35
tags:
 - RocketMQ
categories:
 - RocketMQ
---

# RocketMQ找不到Broker节点

```bash
vim /app/rocketmq/4.9.1/conf/broker.conf
# 添加ip信息
namesrvAddr = 192.168.186.61:9876
brokerIP1 = 192.168.186.61

# 启动时脚本指定配置文件
nohup /app/rocketmq/4.9.1/bin/mqbroker -c /app/rocketmq/4.9.1/conf/broker.conf > /app/rocketmq/4.9.1/logs/broker.log 2>&1 &
```

