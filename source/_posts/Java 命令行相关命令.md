---
title: Java 命令行相关命令
date: 2019-07-22 12:18:59
tags:
 - Java
 - cmd
categories:
 - Java
---

1. 启动时给其设置环境变量. `java -Djava.rmi.server.hostname=45.76.214.173 -jar SSM_WEB_SERVER-1.0-SNAPSHOT.jar `
2. jdk自带的几个小工具的使用:  `jmap`,`jstack`,`jstat`,`jconsole`,`jvisualvm`
- jmap查看内存情况；
- jstack查看线程的堆栈情况；
- jstat查看gc的情况；
- jconsole，jvisualvm综合查看内存，cpu,线程，类的信息；
3. 守护进程运行:
```
nohup java -jar ~/web/academy-tails.jar >/var/log/academy-tails.log 2>&1 &
# 或者
java -jar JrebelLicenseServer.jar -p 12820 &
```



# 更新JAR包中的依赖

> 参考资料: [使用命令动态更新JAR包中的文件](https://www.cnblogs.com/xvpindex/p/9991140.html)

1. 创建要更新的jar包目录: 
![](https://ws1.sinaimg.cn/large/b08fb805gy1g4kfptoqfaj20iu02mjr9.jpg)
![](https://ws1.sinaimg.cn/large/b08fb805gy1g4kfqq3da7j20l105vaa1.jpg)

2. 使用不压缩命令替换文件内容:
```
jar -uvf0 content-microservice.jar BOOT-INF\lib\content-framework-0.0.1.jar
```

# 开启远程服务器`jstatd`
```
/home/icampus3.0/jdk8/bin/jstatd -J-Djava.rmi.server.hostname=192.168.116.190  -J-Djava.security.policy=/home/icampus3.0/jdk8/bin/jstatd.all.policy -p 2099
```

`jstatd.all.policy` 内容
```java
grant codebase "file:${java.home}/../lib/tools.jar" {

   permission java.security.AllPermission;

};
```