---
title: Spring配置
date: 2017-05-23 12:46:18
tags:
 - Spring
 - 配置
categories:
 - Spring
---

## <bean>的 id 和 name 属性区别
- 配置一个bean的时候，我们可以不设置id，也可以不设置name，spring默认会使用类的全限定名作为bean的标识符。
```xml
<bean class="net.aty.spring.ioc.first.C1"></bean> 
```
```java
// 没有设置id,也没有设置name, 默认用类的全限定名  
boolean same = ctx.getBean(C1.class) == ctx.getBean(C1.class  
        .getCanonicalName());  
System.out.println(same);//true  
```
- 如果使用id属性来设置bean的标识符，那么id在spring容器中必需唯一。
- 如果使用name属性来设置，那么设置的其实就是bean的标识符，必需在容器中唯一。
```xml
<bean name="c1" class="net.aty.spring.ioc.first.C1"></bean>  
```
```java
ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext(  
        "first-spring.xml");  
System.out.println(ctx.getBean("c1",C1.class));  
ctx.close();  
```
- 如果同时设置id和name，那么id设置的是标识符，name设置的是别名。
```xml
<bean id="cid" name="alias1" class="net.aty.spring.ioc.first.C1"></bean>  
```
```java
ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext(  
        "first-spring.xml");  
  
String[] alias = ctx.getAliases("cid");  
  
for (String e : alias) {  
    System.out.println(e);//alias1  
}  
  
ctx.close(); 
```
- 如果id和name的值相同，那么spring容器会自动检测并消除冲突：让这个bean只有标识符，而没有别名。
```xml
<bean id="cid" name="cid" class="net.aty.spring.ioc.first.C1"></bean>  
```
```java
ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext(  
        "first-spring.xml");  
  
String[] alias = ctx.getAliases("cid");  
  
System.out.println(alias.length);//0  
  
ctx.close();  
```
- name属性设置多个值。不设置id，那么第一个被用作标识符，其他的被视为别名。如果设置了id，那么name的所有值都是别名。
```xml
<bean name="a1,a2,a3" class="net.aty.spring.ioc.first.C1"></bean>  
```
```java
ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext(  
        "first-spring.xml");  
  
String[] alias = ctx.getAliases("a1");  
  
System.out.println(Arrays.toString(alias));//[a2, a3]  
  
ctx.close();  
```
#### 注意：不管是标识符，还是别名，在容器中必需唯一。因为标识符和别名，都是可以用来获取bean的，如果不唯一，显然不知道获取的到底是哪儿一个bean。

- 使用<alias>标签指定别名，别名也必须在IoC容器中唯一。
```xml
<bean id="c1" class="net.aty.spring.ioc.first.C1"></bean>  
<alias name="c1" alias="1"/>  
<alias name="c1" alias="2"/>  
```
```java
ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext(  
        "first-spring.xml");  
  
String[] alias = ctx.getAliases("c1");  
  
System.out.println(Arrays.toString(alias));//[1,2]  
  
ctx.close(); 
```

#### 总结: 标识符和别名没有任何区别，所以id和name属性唯一的差别在于：id只能设置一个标识符，而name可以设置多个标识符
```xml
<bean id="c1" class="net.aty.spring.ioc.first.C1"></bean>  
<alias name="c1" alias="1"/>  
<alias name="c1" alias="2"/>  
```
```java
System.out.println(Arrays.toString(ctx.getAliases("c1")));//[1,2]  
System.out.println(Arrays.toString(ctx.getAliases("1")));//[c1,2]  
System.out.println(Arrays.toString(ctx.getAliases("2")));//[c1,1]  
  
System.out.println(ctx.getBean("c1") == ctx.getBean("1"));//true  
System.out.println(ctx.getBean("c1") == ctx.getBean("2"));//true  
```
## 配置静态资源后出现异常
1. 异常信息
   >  Failed to convert from type [java.util.ArrayList<?>] to type [java.util.List<org.springframework.core.io.Resource>] for value '[/WEB-INF/static/]'; nested exception is org.springframework.core.convert.ConverterNotFoundException: No converter found capable of converting from type [java.util.ArrayList<?>] to type [org.springframework.core.io.Resource]
2. 原因
   - bean id冲突
   - 同时配置了静态资源 `<mvc:resources>` 其默认bean id为 `conversionService`  
   - spring自动参数绑定并且其bian id为 `<bean id="conversionServiceCustom">`. 