---
title: SpringBoot 整合 Dubbo 
date: 2018-06-30 12:49:08
tags:
 - SpringBoot
 - Dubbo
categories:
 - SpringBoot
---

# 今天完成的事 
Spring Boot 整合 Dubbo 

## Duboo
Duboo 总结, 之前服务端一直无法找到服务提供者, 初步判断是包的兼容性问题, 每个版本示例用的都不一样, 索性换了一个完整的示例. 先运行再看, 大概理解了Dubbo的意思.

1. 服务提供者根据配置文件将要对外发布的 **组件服务(跟SCA的另一种实现, 对我来说用起来比Tuscany舒服多了)的配置**注册到注册中心供服务消费者使用.
2. 服务消费者根据配置文件在注册中心获取 **服务提供者提供的服务信息(服务提供者的服务器ip地址及端口号, 服务的具体名称及包含的方法)**, 再去连接服务器提供者, 调用提供的远程服务(接口)
3. 注册中心起 配置信息存放的作用,  不做具体的数据传输.

**服务提供者**

1. 导入依赖
```xml
<!-- 核心模块，包括自动配置支持、日志和YAML； -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter</artifactId>
</dependency>
<!-- 测试模块，包括JUnit、Hamcrest、Mockito。 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
<!-- Web支持 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<!-- 开发调试支持-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-devtools</artifactId>
    <optional>true</optional>
</dependency>
<!-- http://blog.didispace.com/java-lombok-1/ -->
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
<dependency>
    <groupId>com.gitee.reger</groupId>
    <artifactId>spring-boot-starter-dubbo</artifactId>
    <version>1.0.11</version>
</dependency>
<dependency>
    <groupId>org.apache.zookeeper</groupId>
    <artifactId>zookeeper</artifactId>
    <version>3.4.6</version>
    <exclusions>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
        <exclusion>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

2. 具体实现

启动类 `ml.xiaoweiba.ApplicationDubboServer`
```java
@SpringBootApplication
@Slf4j
public class ApplicationDubboServer {

    // 让 Dubbo 一直循环执行.
    @Bean
    public CountDownLatch closeLatch() {
        return new CountDownLatch(1);
    }

    public static void main(String[] args) {
        SpringApplication.run(ApplicationDubboServer.class, args);
        log.info("Dubbo 服务端启动");
    }
}
```

接口 `ml.xiaoweiba.service.ComputeService`
```java
public interface ComputeService {
    Integer add(int a, int b);
}
```
实现 `ml.xiaoweiba.service.impl.ComputeServiceImpl`
```java
import com.alibaba.dubbo.config.annotation.Service;
import ml.xiaoweiba.service.ComputeService;

/**
 * @program: demo
 * @description: 组件实现类
 * @author: Mr.xweiba
 * @create: 2018-06-30 00:43
 **/

// 添加dubbo service注解 让dubbo扫描到
@Service
public class ComputeServiceImpl implements ComputeService{
    @Override
    public Integer add(int a, int b) {
        return a + b;
    }
}
```

服务发布者配置, `application.yml`
```yml
# Spring-Boot 端口号 防止跟消费者冲突
server:
  port: 8899

spring:
  dubbo:
    application:
      name: provider  # 服务名称
    base-package: ml.xiaoweiba.service  # 扫描@Service注解接口
    registry:  # 注册中心配置 这里使用 zookeeper
      address: 127.0.0.1
      port: 2181
    protocol:   # 使用协议 及 序列化方式
      name: dubbo
      serialization: hessian2
    provider:   # 是否重连, 0 为不重连, 这里让消费者重连
      retries: 0
```

**服务消费者**
1. 导入依赖 (与服务提供者一致, 最好保服务消费者的Dubbo版本与服务提供者一致)
2. 具体实现

启动类 `ml.xiaoweiba.ApplicationDubbo`
```java
import lombok.extern.slf4j.Slf4j;
import org.springframework.boot.CommandLineRunner;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

/**
 * @program: demo
 * @description: Dubbo 服务端启动类
 * @author: Mr.xweiba
 * @create: 2018-06-30 00:18
 **/

// 通过 lombok 直接使用日志
@Slf4j
@SpringBootApplication
public class ApplicationDubbo implements CommandLineRunner {
    public static void main(String[] args) throws InterruptedException {
        SpringApplication.run(ApplicationDubbo.class, args);
    }

    @Override
    public void run(String... strings) throws Exception {
        log.info("服务调用者------>>启动完毕");
    }
}
```

接口 `ml.xiaoweiba.service.ComputeService` 这里是消费者, 接口实现由服务提供者提供
```java
public interface ComputeService {
    Integer add(int a, int b);
}
```

使用服务, 这里用web测试 :
```java
import com.reger.dubbo.annotation.Inject;
import ml.xiaoweiba.service.ComputeService;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

/**
 * @program: demo
 * @description: Dubbo 测试控制器
 * @author: Mr.xweiba
 * @create: 2018-06-30 16:14
 **/

@RestController
public class DubboTest {
    // 使用Dubbo注解来实现远程对象注入
    @Inject
    private ComputeService computeService;
    @RequestMapping(value = "/", method = RequestMethod.GET)
    public String index(){
        return String.valueOf(computeService.add(1,3));
    }
}
```

配置 `application.yml` 
```yml
server:
  port: 8080
spring:
  dubbo:
    application:
      name: consumer
    base-package: ml.xiaoweiba.service
    registry:
      address: 127.0.0.1
      port: 2181

    protocol:
      name: dubbo
      serialization: hessian2

    consumer:
      timeout: 1000
      check: true
      retries: 2
```

**测试**

测试类
```java
import com.reger.dubbo.annotation.Inject;
import org.junit.Assert;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest
public class ComputeServiceTest {

    @Inject
    private ComputeService computeService;

    @Test
    public void add() {
        Assert.assertEquals("compute-service:add", new Integer(3), computeService.add(1, 2));
    }
}
```

web测试:

![mark](http://p9kpdscob.bkt.clouddn.com/screenshot/180702/116JbHaB61.png?imageslim)

**Dubbo-Admin**

官方Github地址: [https://github.com/apache/incubator-dubbo](https://github.com/apache/incubator-dubbo)

git下来后配置好直接生成jar包运行即可. 

配置: `application.properties`
```properties
server.port=7001
spring.velocity.cache=false
spring.velocity.charset=UTF-8
spring.velocity.layout-url=/templates/default.vm
spring.messages.fallback-to-system-locale=false
spring.messages.basename=i18n/message
# 注意 这里是设置root用户的密码
spring.root.password=root
# 注意 这里是设置guest用户的密码
spring.guest.password=guest

dubbo.registry.address=zookeeper://127.0.0.1:2181
```

消费者和服务者启动后即可再web页面管理它:

![mark](http://p9kpdscob.bkt.clouddn.com/screenshot/180702/Dc5iIg2dfl.png?imageslim)

![mark](http://p9kpdscob.bkt.clouddn.com/screenshot/180702/k29Hj5IkK8.png?imageslim)

![mark](http://p9kpdscob.bkt.clouddn.com/screenshot/180702/DddgAb7003.png?imageslim)

# 明天计划的事:
spring-boot 整合 shiro