---
title: SpringBoot Thymeleaf  模板
date: 2018-06-28 12:35:45
tags:
 - SpringBoot
 - Thymeleaf
categories:
 - SpringBoot
---

配置说明: 

配置一切正常, 但一直 404 问题, [解决办法](https://segmentfault.com/q/1010000010541125)

原因, maven仓库包下载的问题
```
按照在Stack Overflow上一位外国朋友遇到的差不多的问题以及解答，说是有可能是maven引入的包有问题导致的。但是该问题无法通过在项目中清理后重新构建来修复（本人尝试无用），只能把配置的对应的maven m2目录清空，然后打开eclipse以后重新构建的时候让maven重新下载所需jar包才可以。
```

解决方法

- 删除原来的maven仓库, 有很多个人包, 不方便
- 手动指定Thymeleaf版本

```xml
<properties>
    <thymeleaf.version>3.0.2.RELEASE</thymeleaf.version>
    <thymeleaf-layout-dialect.version>2.1.1</thymeleaf-layout-dialect.version>
</properties>
```

支持JSP的配置

Spring Boot 不建议使用Jsp，但如果一定要使用，可以参考此工程作为脚手架：[JSP支持](https://github.com/spring-projects/spring-boot/tree/v1.3.2.RELEASE/spring-boot-samples/spring-boot-sample-web-jsp)