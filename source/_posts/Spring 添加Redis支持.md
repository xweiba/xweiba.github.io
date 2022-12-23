---
title: Spring 添加Redis支持
date: 2017-05-26 12:44:58
tags:
 - Spring
 - Redis
categories:
 - Spring 
---

## Maven 包
```xml
    <!-- redis -->
        <!-- redis 驱动-->
        <dependency>
            <groupId>redis.clients</groupId>
            <artifactId>jedis</artifactId>
            <version>2.9.0</version>
        </dependency>
        <!-- spring redis数据支持包 spring-data-redis 2.0后版本只支持spring5和spring boot2 -->
        <dependency>
            <groupId>org.springframework.data</groupId>
            <artifactId>spring-data-redis</artifactId>
            <version>1.8.6.RELEASE</version>
        </dependency>
    <!-- redis end -->
```
### 依赖冲突
1. 在mavne仓库里面,查找你的jar相关的版本
   >地址: http://mvnrepository.com/   
   >点开相关版本,查看Compile Dependencies 列表,会显示此包所依赖的相关版本信息.
2. spring-data-redis依赖
   > 其最低的spring依赖版本为4.3.10.RELEASE,将spring版本升级到到4.3.10
3. 升级spring版本后AOP的依赖又出现了问题. 心累
   >报错异常 :
   >Expected raw type form of org.springframework.web.servlet.handler.AbstractHandlerMethodMapping$MappingRegistry
3. 原因是spring 4.3.10以上 aspectJ 版本必须高于1.8.9, 我升级到1.8.11正常.

## 配置
1. redis连接配置 redis.properties
    > redis.host=127.0.0.1  
    > redis.port=6379  
    > redis.pass=  
    > redis.maxIdle=300  
    > redis.maxWait=1000  
    > redis.testOnBorrow=true  
2. 添加redis spring配置 `applicationContenxt-redis.xml
   - 导入redis连接配置 
     ```xml
     <context:property-placeholder location="WEB-INF/config/other/redis.properties"/>
     ```
    - 报错 `Could not resolve placeholder` [参考连接](https://blog.csdn.net/linwei_1029/article/details/6873764)  
      > 因为使用了多个PropertyPlaceholderConfigurer或者多个<context:property-placeholder>的原因。  
      > 一定要记住，不管是在一个Spring文件还是在多个Spring文件被统一load的情况下，直接写多个都是不允许的. 
   
    - 解决方法
      1. 解决方法一, 定义一个全局context导入不同配置
        ```xml
        <context-param>  
            <param-name>contextConfigLocation</param-name>  
            <param-value>  
                    WEB-INF/config/spring/dao.xml,   
                    WEB-INF/config/spring/dfs.xml  
            </param-value>  
        </context-param>  
        ```
      2. 解决方法二, 每资源导入时都要加上`ignore-unresolvable="true"`参数，一个加另一个不加也是不行的
      ```xml
      <context:property-placeholder location="xxx.properties" ignore-unresolvable="true"/>  
      <context:property-placeholder location="yyy.properties" ignore-unresolvable="true" />  
   - 注入连接池配置  [testOnBorrow作用](https://blog.csdn.net/wangyangzhizhou/article/details/52209336)
     ```xml
     <!-- 注入连接池配置 -->
     <bean id="redis-config" class="redis.clients.jedis.JedisPoolConfig">
        <!-- 最大空闲数 -->
        <property name="maxIdle" value="${redis.maxIdle}"/>
        <!-- 最大等待时间 -->
        <property name="maxWaitMillis" value="${redis.maxWait}"/>
        <!-- 是否检测连接池的可用性 -->
        <property name="testOnBorrow" value="${redis.testOnBorrow}"/>
     </bean>
     ```
     [参考连接](https://blog.csdn.net/qq_34021712/article/details/75949706)