---
title: SpringBoot 1.5.xx 升级至 2.xx
date: 2020-08-20 12:29:14
tags:
 - SpringBoot
 - 升级
categories:
 - SpringBoot
---

# 升级依赖调整记录

## 一. Maven 调整
### redis:
升级前:
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-redis</artifactId>
</dependency>
```
升级后:
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

### thymeleaf-extras-springsecurity:
升级前:
```
<dependency>
    <groupId>org.thymeleaf.extras</groupId>
    <artifactId>thymeleaf-extras-springsecurity4</artifactId>
</dependency>
```
升级后
```
<dependency>
    <groupId>org.thymeleaf.extras</groupId>
    <artifactId>thymeleaf-extras-springsecurity5</artifactId>
</dependency>
```

### Feign Options 找不到构造方法:
`Maven` 搜索 `feign-core` 依赖, 升级或排除 `netflix.feign` 包的模块.


## 二. yml 配置文件调整
### redis:
升级前:
```yml
spring:
    redis:
        host: 192.168.116.190
        password: ischool20
        port: 9779
        pool:
          max-idle: 100
          min-idle: 1
          max-active: 100
          max-wait: -1
```
升级后:
```yml
spring:
    redis:
        host: 192.168.116.190
        password: ischool20
        port: 9779
        lettuce:
          pool:
            max-idle: 8
            min-idle: 0
            max-active: 8
            max-wait: -1
```
### `feign` 重复注入
feign升级后默认要求每一个 `@FeignClient` 都要有单独的实例名称，可添加配置允许覆盖，如下
```
spring:  
  main:
     allow-bean-definition-overriding: true
```

## 三. 代码变更
### `SDK` 包名修改.
### redis:
升级前
```java
@Bean
public CacheManager cacheManager(RedisTemplate redisTemplate) {
    RedisCacheManager cacheManager= new RedisCacheManager(redisTemplate);
    cacheManager.setDefaultExpiration(30 * 60L);
    cacheManager.setUsePrefix(true);
    return cacheManager;
}
```
升级后:
```java
@Bean
public CacheManager cacheManager(RedisConnectionFactory factory) {
	RedisCacheConfiguration redisCacheConfiguration = RedisCacheConfiguration.defaultCacheConfig()
			.entryTtl(Duration.ofMillis(30)); // UsePrefix默认为true
    return RedisCacheManager
			.builder(RedisCacheWriter.nonLockingRedisCacheWriter(factory))
			.cacheDefaults(redisCacheConfiguration).build();
}
```
### junit:
`org.junit.Assert` 包更名为: `org.junit.jupiter.api.Assertions`, 部分参数需要修改, 变化不大. `message` 统一调至最后一个参数.

### EasyPoi 升/降级 对应变化常量参数:
| 属性       | 3.9 版本                                   | 3.15 版本                        |
| ---------- | ------------------------------------------ | -------------------------------- |
| 填充模式   | CellStyle.SOLID_FOREGROUND                 | FillPatternType.SOLID_FOREGROUND |
| 边框间距   | HSSFCellStyle.BORDER_THIN                  | BorderStyle.THIN                 |
| 居中       | XSSFCellStyle.ALIGN_CENTER                 | HorizontalAlignment.CENTER       |
| 垂直       | XSSFCellStyle.VERTICAL_CENTER              | VerticalAlignment.CENTER         |
| 字体加粗   | font.setBoldweight(Font.BOLDWEIGHT_BOLD)   | font.setBold(true)               |
| 字体不加粗 | font.setBoldweight(Font.BOLDWEIGHT_NORMAL) | font.setBold(false)              |
| 是否验证   | setNeedVerify                              | setNeedVerfiy                    |

### es jest 
`springBoot 2.3.0`版本及以后版本不支持`es`查询工具`jestClient`自动注入, 手动注入
```java
@Repository
public class JestInit {

    @Bean("jestClient")
    public JestClient getJestCline(){
        JestClientFactory factory = new JestClientFactory();
        factory.setHttpClientConfig(new HttpClientConfig
                .Builder("http://localhost:9200")
                .multiThreaded(true)
                .build());
        return  factory.getObject();
    }
}
```

### 报错
`Caused by: java.lang.NoClassDefFoundError: Could not initialize class org.hibernate.validator.internal.engine.ConfigurationImpl` 错误, 这是`springboot2.3`以上自带的, 手动导入`hibernate` 依赖:
```
<dependency>
    <groupId>org.hibernate.validator</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>6.1.5.Final</version>
    <scope>compile</scope>
</dependency>
```

## swagger 无接口数据
升级后需要在配置文件中开启`swagger`, 默认是关闭的.
```
application:
  swagger:
    enabled: true
```