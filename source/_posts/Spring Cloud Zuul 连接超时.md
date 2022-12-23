---
title: SpringCloud Zuul 连接超时
date: 2019-07-12 12:41:55
tags:
 - SpringCloud
 - Zuul
categories:
 - SpringCloud
---

错误异常:
```
"error":"Internal Server Error","exception":"com.netflix.zuul.exception.ZuulException","message":"GENERAL"
```

zuul 服务添加配置: 
```
# 负载均衡配置
ribbon:
  # 25 秒超时
  ReadTimeout: 25000
  ConnectTimeout: 6000
  MaxAutoRetries: 0
  MaxAutoRetriesNextServer: 1
  eureka:
    enabled: true

# 熔断器配置
hystrix:
  command:
    default:
      execution:
        isolation:
          thread:
            timeoutInMilliseconds: 60000
```