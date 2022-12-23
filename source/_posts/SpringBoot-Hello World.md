---
title: Spring Boot-Hello World
date: 2018-06-18 12:28:30
tags:
 - SpringBoot
 - hello world
categories:

 - SpringBoot
---

本文学习自 [纯洁的微笑 Spring Boot 教程](https://github.com/ityouknow/spring-boot-examples)

Spring Boot 示例程序: 

## maven构建项目
**下载Spring Boot模板:**

1. 访问http://start.spring.io/
2. 选择构建工具Maven Projec, 其他默认即可, 
3. 点击Generate Project下载项目压缩包
4. 下载后解压, 再使用idea打开

**项目结构介绍:**

spingboot建议的目录结果如下：

```
com
  +- example
    +- myproject
      +- Application.java
      |
      +- domain
      |  +- Customer.java
      |  +- CustomerRepository.java
      |
      +- service
      |  +- CustomerService.java
      |
      +- controller
      |  +- CustomerController.java
      |
```

1. **Application.java 建议放到根目录下面,主要用于做一些框架配置 (它是Spring Boot的main方法, 启动时只会扫描以它为根目录一下的文件)**
2. domain目录主要用于实体（Entity）与数据访问层（Repository）
3. service 层主要是业务类代码
4. controller 负责页面访问控制

采用默认配置可以省去很多配置，当然也可以根据自己的喜欢来进行更改
最后，启动Application main方法，至此一个java项目搭建好了！


**pom.xml文件中默认有两个模块**：

spring-boot-starter: 核心模块，包括自动配置支持、日志和YAML；

spring-boot-starter-test: 测试模块，包括JUnit、Hamcrest、Mockito。

**pom.xml 中添加spring boot Web支持:**

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

**在项目中添加一个controller**

`@RestController` :
1. 代表这个Controller类所有的handler都以Json格式返回数据, 不需要在对接的方法添加@ResponseBody
2. 不需要你去导入JSON等依赖
3. 不需要配置 spring controller扫描

\*. 如果我们需要使用页面开发只要使用 `@Controller`

```java
@RestController
public class HelloWorldController {
    @RequestMapping("/")
    public String index() {
        return "Hello World";
    }
}
```

启动main方法, 就可以正常访问 http://127.0.0.1:8080, 不需要任何其他配置. 

测试类: 
```java
package com.example.demo;

import com.example.demo.controller.HelloWorldController;
import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.MediaType;
import org.springframework.test.context.junit4.SpringRunner;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;

import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@RunWith(SpringRunner.class)
@SpringBootTest
public class DemoApplicationTests {

    private MockMvc mockMvc;

    @Before
    public void setMvc() {
        // 启动Spring Boot
        mockMvc = MockMvcBuilders.standaloneSetup(new HelloWorldController()).build();
    }

    @Test
    public void getMvc() throws Exception {
        // perform 执行 ->  
        // MockMvcRequestBuilders.get("/") 请求 ->
        // 接收数据accept, 接收的格式为:MediaType.APPLICATION_JSON -> 
        // 对比 andExpect(), 条件为: 返回状态码 status() 是否为正常获取: isOk() ->
        // 对比 andExpect(), 条件为: 返回内容 content() 是否为: string("Hello World").
        mockMvc.perform(MockMvcRequestBuilders.get("/").accept(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk())
                .andExpect(content().string("Hello World"));
    }
}

```

**开发环境的调试**

热启动在正常开发项目中已经很常见了吧，虽然平时开发web项目过程中，改动项目启重启总是报错；但springBoot对调试支持很好，修改之后可以实时生效，需要添加以下的配置：

```
 <dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-devtools</artifactId>
        <optional>true</optional>
    </dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
            <configuration>
                <fork>true</fork>
            </configuration>
        </plugin>
</plugins>
</build>
```

该模块在完整的打包环境下运行的时候会被禁用。如果你使用java -jar启动应用或者用一个特定的classloader启动，它会认为这是一个“生产环境”。