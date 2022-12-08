---
title: 手写代码模拟Spring底层原理
date: 2022-12-08 15:06:03
tags:
 - Spring
categories:
 - Spring
---

通过 `Spring` 核心原理解析已经大致了解了 `Bean` 的创建过程, 今天来尝试手写实现一下.

# 实现目标

1. 可以通过注解注入 `Bean`
2. 实现通过容器的 `getBean` 方法获取 `Bean` 实例
3. 单例和多例 `bean` 的实现
4. `CGLIB动态代理Bean` 的实现

# 创建基本的类和注解

1. 在 `com.spring` 包下分别创建 `WbAnnotationConfigApplicationContent`, `@ComponentSacn`,  `@Component`, `@Autowired` 注解

   ```java
   package com.spring;
   
   /**
    * @author wb
    **/
   
   public class WbAnnotationSpringApplication {
   
       public WbAnnotationSpringApplication(Class clazz) {
   
       }
   
       public Object getBean(String beanName) {
           return null;
       }
   }
   
   
   package com.spring;
   
   /**
    * @author wb
    **/
   public @interface ComponentScan {
   
       String value() default "";
   
   }
   
   
   package com.spring;
   
   public @interface Component {
   
       String value() default "";
   
   }
   
   package com.spring;
   
   public @interface Autowired {
   
       String value() default "";
   
   }
   ```

2. 在 `con.wb` 下创建我们自己代码 `AppConfig` ,`UserInfoService` , `OrderInfoService`, `WbTest`

   ```java
   package com.wb;
   
   import com.spring.WbAnnotationSpringApplication;
   
   /**
    * @author wb
    **/
   
   public class WbTest {
   
       public static void main(String[] args) {
   
           WbAnnotationSpringApplication context = new WbAnnotationSpringApplication(AppConfig.class);
   
           UserInfoService userInfoService = (UserInfoService) context.getBean("userInfoService");
           userInfoService.test();
       }
   }
   
   
   package com.wb.service;
   
   
   @Component
   public class UserInfoService {
   
       @Autowired
       private OrderInfoService orderInfoService;
   
       public void test(){
           System.out.println(orderInfoService);
       }
   
   }
   
   
   package com.wb.service;
   
   public class OrderInfoService {
   
   }
   
   package com.wb;
   
   /**
    * @author wb
    **/
   @ComponentScan
   public class AppConfig {
   
   }
   ```

项目初始化完毕.

# 分析 `Spring` 中的 `AnnotationSpringApplication` 做了什么?

1. 通过 `AppConfig` 类上的 `@ComponentScan` 注解扫描其 `value` 值中的包.
2. 将扫描包中包含 `@Component` 注解的类通过一定的方法注入到 `Spring` 容器中.

... 待更新

