---
title: SpringBoot - Redis
date: 2018-06-22 12:32:13
tags:
 - SpringBoot
 - Redis
categories:
 - SpringBoot
---

# 今天完成的事情：
Spring Boot Redis, 依赖的事情弄了很久, 纯洁的微笑的示例教程是使用1.0的Spring Boot, 我使用的是2.0.1, 有很大出入, 它Github上的1.5.6版本的Redis示例也是有问题的.

## Spring Boot Redis
如何使用

引入 redis 相关依赖, 注意:
```xml                        , 
<!-- Spring Boot使用的是1.4（包括1.4版本）之前的版本使用如下配置 -->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-redis</artifactId>
</dependency>
<!-- 1.5.* 的版本需要指定版本号 -->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-redis</artifactId>
   <version>1.4.6.RELEASE</version>
</dependency>
<!-- 2.* 版本 redis依赖改名了,直接使用下面的依赖 -->
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
<!-- 导入其他模块的依赖 -->
<dependency>
   <groupId>com.example</groupId>
   <artifactId>02-SpringBootWeb</artifactId>
   <version>0.0.1-SNAPSHOT</version>
</dependency>
```
在配置文件中添加配置

```
#Redis ProPerTies

#Redis数据库索引(默认为0)
spring.redis.database=0
#Redis服务器地址
spring.redis.host=127.0.0.1
#Redis服务器端口号
spring.redis.port=6379
#Redis服务器密码(默认为空)
spring.redis.password=
#Redis 连接池最大连接数(使用负值表示没有限制)
spring.redis.jedis.pool.max-active=8
#Redis 连接池最大阻塞时间(使用负数表示没有限制)
spring.redis.jedis.pool.max-wait=-1
#Redis 连接池中最大的空闲连接
spring.redis.jedis.pool.max-idle=8
#Redis 连接池中最小的空间连接
spring.redis.jedis.pool.min-idle=0
#Redis 连接超时时间
spring.redis.timeout=0
```
代码实现: 
```java
ml.xiaoweiba.cache.RedisConfig

package ml.xiaoweiba.cache;

import com.fasterxml.jackson.annotation.JsonAutoDetect;
import com.fasterxml.jackson.annotation.PropertyAccessor;
import com.fasterxml.jackson.databind.ObjectMapper;
import org.springframework.cache.CacheManager;
import org.springframework.cache.annotation.CachingConfigurerSupport;
import org.springframework.cache.annotation.EnableCaching;
import org.springframework.cache.interceptor.KeyGenerator;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.data.redis.cache.RedisCacheManager;
import org.springframework.data.redis.connection.RedisConnectionFactory;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.data.redis.serializer.Jackson2JsonRedisSerializer;


import java.lang.reflect.Method;



/**
* @program: demo
* @description: Redis Cache 配置类
* @author: Mr.xweiba
* @create: 2018-06-22 11:13
**/

@Configuration
@EnableCaching
public class RedisConfig extends CachingConfigurerSupport {

   @Bean
   public KeyGenerator keyGenerator() {
       return new KeyGenerator() {
           @Override
           public Object generate(Object target, Method method, Object... params) {
               StringBuilder sb = new StringBuilder();
               sb.append(target.getClass().getName());
               sb.append(method.getName());
               for (Object obj : params) {
                   sb.append(obj.toString());
               }
               return sb.toString();
           }
       };
   }

   @SuppressWarnings("rawtypes")
   @Bean
   public CacheManager cacheManager(RedisTemplate redisTemplate) {
       RedisCacheManager rcm = new RedisCacheManager(redisTemplate);
       //设置缓存过期时间
       //rcm.setDefaultExpiration(60);//秒
       return rcm;
   }


   @Bean(name="redisTemplate")
   public RedisTemplate<String, String> redisTemplate(RedisConnectionFactory factory) {
       StringRedisTemplate template = new StringRedisTemplate(factory);
       Jackson2JsonRedisSerializer jackson2JsonRedisSerializer = new Jackson2JsonRedisSerializer(Object.class);
       ObjectMapper om = new ObjectMapper();
       om.setVisibility(PropertyAccessor.ALL, JsonAutoDetect.Visibility.ANY);
       om.enableDefaultTyping(ObjectMapper.DefaultTyping.NON_FINAL);
       jackson2JsonRedisSerializer.setObjectMapper(om);
       template.setValueSerializer(jackson2JsonRedisSerializer);
       template.afterPropertiesSet();
       return template;
   }
}
```

测试类:
```java
package ml.xiaoweiba.cache;

import java.util.concurrent.TimeUnit;

import ml.xiaoweiba.entity.Student;
import org.junit.Assert;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration;
import org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.data.redis.core.RedisTemplate;
import org.springframework.data.redis.core.StringRedisTemplate;
import org.springframework.data.redis.core.ValueOperations;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;


@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootApplication(exclude={DataSourceAutoConfiguration.class,HibernateJpaAutoConfiguration.class})
@SpringBootTest
public class RedisConfigTest {
   @Autowired
   private StringRedisTemplate stringRedisTemplate;

   @Autowired
   private RedisTemplate redisTemplate;

   @Test
   public void test() throws Exception {
       stringRedisTemplate.opsForValue().set("aaa", "111");
       Assert.assertEquals("111", stringRedisTemplate.opsForValue().get("aaa"));
   }

   @Test
   public void testObj() throws Exception {
       Student student = new Student("aa", 24, "aa@126.com", "213321");
       ValueOperations<String, Student> operations = redisTemplate.opsForValue();
       operations.set("students", student);
       operations.set("studentOne", student, 1, TimeUnit.SECONDS);
       Thread.sleep(1000);
       //redisTemplate.delete("com.neo.f");
       boolean exists = redisTemplate.hasKey("studentOne");
       if (exists) {
           System.out.println("exists is true");
       } else {
           System.out.println("exists is false");
       }
       Assert.assertEquals("aa", operations.get("students").getStudentName());
   }

}
```

## 错误
测试报错:

`If you have database settings to be loaded from a particular profile you may need to active it`

导致此问题的原因为，springboot生成的项目启动时会自动注入数据源。而此时在配置文件中并没有配置数据源信息，因此会抛出异常

（1）如果暂时不需要数据源，可将pom文件中的mysql和mybatis（或其他数据源框架）注释掉，即可正常启动。

（2）在@SpringBootApplication中排除其注入,( 这里使用这个方法解决 )

`@SpringBootApplication(exclude={DataSourceAutoConfiguration.class,HibernateJpaAutoConfiguration.class})`

（3）提供数据源的配置或其他数据源配置，此处提供默认配置示例，在application.properties文件中添加以下配置项：

### 主数据源，默认的
```
#spring.datasource.type=com.zaxxer.hikari.HikariDataSource

spring.datasource.driverClassName=com.mysql.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/test
spring.datasource.username=root
spring.datasource.password=root
```

## 注解
@EnableCaching
Spring 3开始提供的通过注解开启缓存功能

# 明天计划的事情：
继续Spring Boot, 今天弄清楚Redis和Spring Boot 1.* 2.* 之间的关系花了很久, 明天完成redis 在Spring Boot 2.0版本下的应用.