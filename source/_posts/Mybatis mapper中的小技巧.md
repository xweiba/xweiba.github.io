---
title: Mybatis mapper中的小技巧
date: 2019-08-23 12:51:02
tags:
 - Mybatis
 - 经验
categories:
 - Mybatis
---

# 小技巧
当只传入一个变量时, 可以使用_parameter来指代它  
这里的_parameter就代表传入的parameterType的值

```xml
<select id="queryWork" parameterType="int" resultType="int">
   SELECT COUNT(isWork) FROM student
<if test="_parameter!=0">
       where isWork=#{isWork};
</if>
</select>
```

传入对象中有Integ[]数组时, 通过 ${par[0]} 下标取值

# 添加 `sql` 日志
配置文件添加

```
mybatis:
  config-location: classpath:mybatis-config.xml
```

mybatis-config.xml 添加

```
<settings>
    <setting name="logImpl" value="STDOUT_LOGGING"/>
</settings>
```

不显示的话直接在`logback.xml`中添加:

```
<logger name="xxx.dao" level="debug"/>
```