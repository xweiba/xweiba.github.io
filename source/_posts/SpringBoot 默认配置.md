---
title: SpringBoot 默认配置
date: 2018-02-23 12:34:21
tags:
 - SpringBoot
 - 配置
categories:
 - SpringBoot
---

```yml
# 1984组 求学大作战后台服务 academy_canary_admin_web
# 开发环境配置
# Spring 设置
spring:
  datasource:
    # 连接池
    type: com.alibaba.druid.pool.DruidDataSource
    # 数据库驱动
    driver-class-name: com.mysql.jdbc.Driver
    # 数据库url
    url: jdbc:mysql://localhost:3306/canary?useUnicode=true&characterEncoding=utf-8&autoReconnect=true&zeroDateTimeBehavior=convertToNull&allowMultiQueries=true&useSSL=false
    username: test
    password: test123456
  application:
    name: academy-canary
# HTTP ENCODING
  http:
      multipart:
          max-file-size: 2MB
          max-request-size: 10MB
      encoding:
          enabled: true
          charset: UTF-8
          force: true
  messages:
      encoding: UTF-8
  jmx:
      enabled: true
      default-domain: agentservice
  resources:
      chain:
          strategy:
              content:
                  enabled: true
                  paths: /**
server:
  context-path: /a
  port: 20475
  # HTTP请求和响应头的最大量, 以字节为单位, 默认值为4096, 超过此长度的部分不予处理, 一般为8k. 解决java.io.EOFException: null问题
  max-http-header-size: 8192
  # 请求是否允许X-Forwarded-*
  use-forward-headers: true
  compression:
    # 是否开启压缩，默认为false.
    enabled: true
    # 执行压缩的阈值，默认为2048
    min-response-size: 1024
    # 指定要压缩的MIME type，多个以逗号分隔.
    mime-types: text/plain,text/css,text/xml,text/javascript,application/json,application/javascript,application/xml,application/xml+rss,application/x-javascript,application/x-httpd-php,image/jpeg,image/gif,image/png
  tomcat:
    # 设定remote IP的header，如果remoteIpHeader有值，则设置为RemoteIpValve
    remote-ip-header: X-Forwarded-for
    # 设定Header包含的协议，通常是 X-Forwarded-Proto，如果remoteIpHeader有值，则将设置为RemoteIpValve.
    protocol-header: X-Forwarded-Proto
    # 设定http header使用的，用来覆盖原来port的value.
    port-header: X-Forwarded-Port
    # 设定URI的解码字符集.
    uri-encoding: UTF-8
    # 存放Tomcat的日志、Dump等文件的临时文件夹，默认为系统的tmp文件夹（如：C:\Users\Shanhy\AppData\Local\Temp）
    basedir: /var/tmp/website-app

# MyBatis
mybatis:
    # 多个包加 ,
    type-aliases-package: com.ptteng.academy.persistence.beans,com.ptteng.academy.business.dto,com.ptteng.academy.business.query
    mapper-locations: classpath:/mybatis/*.xml
# mapper
mapper:
    mappers:
        - com.ptteng.academy.plugin.BaseMapper
    not-empty: false
    identity: MYSQL
# pagehelper
pagehelper:
    helper-dialect: mysql
    reasonable: true
    support-methods-arguments: true
    params: count=countSql
banner:
    charset: UTF-8

canary:
  druid:
    # druid访问用户名（默认：test）
    username: test
    # druid访问密码（默认：zyd-druid）
    password: test
    # druid访问地址（默认：/druid/*）
    servletPath: /druid/*
    # 启用重置功能（默认false）
    resetEnable: false
    # 白名单(非必填)， list
    allowIps[0]:
    # 黑名单(非必填)， list
    denyIps[0]:

# 开启mysql sql 日志
logging:
  level:
    com.ptteng.academy.persistence.mapper: DEBUG
```

springboot 导入外部配置, core定义全局配置:
```yml
spring:
  profiles:
    # 引入核心配置
    include: core
```