---
title: SpringBoot Web过滤器Filter
date: 2018-06-29 12:38:53
tags:
 - SpringBoot
 - 过滤器
categories:
 - SpringBoot 
---

## Spring Boot Web相关示例

### 过滤器
tomcat 提供的过滤器 : RemoteIpFilter.

[Spring Boot：定制servlet filters] https://www.ktanx.com/blog/p/4237



### 自定义配置 Property

配置在application.properties中即可, Spring Boot默认都是读取该配置的. 

**设置自定义值**

```
com.neo.username=测试
com.neo.age=34
```
```
@Component
public class User implements Serializable{
    private static final long serialVersionUID = 8667468581448787106L;

    @Value("${com.neo.username}")
    private String sUsername;
    @Value("${com.neo.age}")
    private int iAge;
}
```

**log配置**
```
#window下这样的路径是直接再c盘下新建的
logging.path=/user/local/log
logging.level.com.favorites=DEBUG
logging.level.org.springframework.web=INFO
logging.level.org.hibernate=ERROR
```
path为本机的log地址，logging.level  后面可以根据包路径配置不同资源的log级别

### 数据库操作
在这里我重点讲述mysql、spring data jpa的使用，其中mysql 就不用说了大家很熟悉，jpa是利用Hibernate生成各种自动化的sql，如果只是简单的增删改查，基本上不用手写了，spring内部已经帮大家封装实现了。

下面简单介绍一下如何在spring boot中使用
```
# 数据库连接配置
spring.datasource.url=jdbc:mysql://localhost:3306/test  
spring.datasource.data-username=test  
spring.datasource.data-password=test123456  
spring.datasource.driver-class-name=com.mysql.jdbc.Driver  

# Spring date jpa
spring.jpa.properties.hibernate.hbm2ddl.auto=update  
```

hibernate.hbm2ddl.auto参数的作用主要用于：自动创建|更新|验证数据库表结构,有四个值：

- create： 每次加载hibernate时都会删除上一次的生成的表，然后根据你的model类再重新来生成新表，哪怕两次没有任何改变也要这样执行，这就是导致数据库表数据丢失的一个重要原因。

- create-drop ：每次加载hibernate时根据model类生成表，但是sessionFactory一关闭,表就自动删除。

- update：最常用的属性，第一次加载hibernate时根据model类会自动建立起表的结构（前提是先建立好数据库），以后加载hibernate时根据 model类自动更新表结构，即使表结构改变了但表中的行仍然存在不会删除以前的行。要注意的是当部署到服务器后，表结构是不会被马上建立起来的，是要等 应用第一次运行起来后才会。

- validate ：每次加载hibernate时，验证创建数据库表结构，只会和数据库中的表进行比较，不会创建新表，但是会插入新值。