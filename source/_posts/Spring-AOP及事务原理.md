---
title: Spring AOP及事务原理
date: 2022-04-07 10:59:57
tags:
 - AOP
 - 事务
categories:
 - Java
---

## 相关笔记

`@EnableAspectJAutoProxy(exposeProxy=true)` 开启动态代理的暴露，因为在 `JDK` 动态代理模式下，在方法中调用本类方法是无法再次走代理的，试用该注解可以将动态代理暴露到当前线程当中，在方法内需要再次走代理时通过 `(XXXService)AopContext.currentProxy()` 即可取出当前线程的动态代理对象，通过它调用本类方法即可再次走代理做增强操作

