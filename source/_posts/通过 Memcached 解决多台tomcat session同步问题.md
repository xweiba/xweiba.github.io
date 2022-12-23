---
title: 通过 Memcached 解决多台tomcat session同步问题
date: 2018-07-26 13:34:36
tags:
 - session
 - tomcat
categories:
 - Memcached
---

## 准备工作
- Nginx 负载均衡已配置
- Tomcat 已开启多个容器
- Tomcat 8
- 官方文档: `https://github.com/magro/memcached-session-manager/wiki/SetupAndConfiguration`
- 参考资料: `https://www.cnblogs.com/jsonhc/p/7344902.html`
- 
## 下载所需jar包, 每个tomcat版本对应的包不同
- `wget http://repo1.maven.org/maven2/de/javakaffee/msm/memcached-session-manager/2.1.1/memcached-session-manager-2.1.1.jar`
- `wget http://repo1.maven.org/maven2/de/javakaffee/msm/memcached-session-manager-tc8/2.1.1/memcached-session-manager-tc8-2.1.1.jar`
- `wget http://repo1.maven.org/maven2/net/spy/spymemcached/2.11.1/spymemcached-2.11.1.jar`
- 如果仅仅只是用java来做序列化器只需上面这三个包就ok
- 下面两个包,配置后一直报错,未测试通过 
- `wget http://repo1.maven.org/maven2/de/javakaffee/msm/msm-javolution-serializer/1.8.3/msm-javolution-serializer-1.8.3.jar`
- `wget http://repo1.maven.org/maven2/de/javakaffee/msm/msm-javolution-serializer/2.1.1/msm-javolution-serializer-2.1.1.jar`
- 下载完成后放入tomcat/lib目录中

## 修改两个tomcat配置文件
- vim `{tomcat}/conf/context.xml`

    ```sh
    <Context>
        <!-- web application will be reloaded.                                   -->
        <WatchedResource>WEB-INF/web.xml</WatchedResource>
        <WatchedResource>${catalina.base}/conf/web.xml</WatchedResource>
    
        <!-- Uncomment this to disable session persistence across Tomcat restarts -->
        <!--
        <Manager pathname="" />
        -->
        <Manager className="de.javakaffee.web.msm.MemcachedBackupSessionManager"
        memcachedNodes="n1:127.0.0.1:11211,n2:127.0.0.1:22122"
        failoverNodes="n1"
        requestUriIgnorePattern=".*\.(ico|png|gif|jpg|css|js)$"
        />
    </Context>
    ```