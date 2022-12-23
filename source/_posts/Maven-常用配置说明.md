---
title: Maven-常用配置说明
date: 2017-11-23 13:36:29
tags:
 - Maven
 - 配置
categories:
 - Maven
---

### 全局版本号

```xml
<properties>
    <!-- 设置编码 -->
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <spring.version>4.3.14.RELEASE</spring.version>
    <!--log4j日志包版本号-->
    <slf4j.version>1.7.18</slf4j.version>
    <log4j.version>1.2.17</log4j.version>
  </properties>
```

### 依赖
```xml
<dependencies>
    <!-- redis  -->
    <!-- redis 连接驱动-->
    <dependency>
        <groupId>redis.clients</groupId>
        <artifactId>jedis</artifactId>
        <version>2.9.0</version>
    </dependency>
    <!-- spring 支持包 -->
    <dependency>
        <groupId>org.springframework.data</groupId>
        <artifactId>spring-data-redis</artifactId>
        <version>2.0.7.RELEASE</version>
    </dependency>
    <!-- redis end -->
  	<!-- memcached -->
        <dependency>
            <groupId>com.whalin</groupId>
            <artifactId>Memcached-Java-Client</artifactId>
            <version>3.0.2</version>
        </dependency>
        <!-- end -->
        <!-- 对象池 -->
        <dependency>
            <groupId>commons-pool</groupId>
            <artifactId>commons-pool</artifactId>
            <version>1.6</version>
        </dependency>
        <!-- end -->
  	<!-- encodebase64string -->
        <dependency>
            <groupId>commons-net</groupId>
            <artifactId>commons-net</artifactId>
            <version>3.3</version>
        </dependency>
        <!-- end -->
  	<!-- Tiles 包-->
        <dependency>
            <groupId>org.apache.tiles</groupId>
            <artifactId>tiles-api</artifactId>
            <version>2.2.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.tiles</groupId>
            <artifactId>tiles-core</artifactId>
            <version>2.2.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.tiles</groupId>
            <artifactId>tiles-jsp</artifactId>
            <version>2.2.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.tiles</groupId>
            <artifactId>tiles-servlet</artifactId>
            <version>2.2.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.tiles</groupId>
            <artifactId>tiles-template</artifactId>
            <version>2.2.1</version>
        </dependency>
        <!-- Tiles end -->
  	
      <!-- mysql -->
      <dependency>
          <groupId>mysql</groupId>
          <artifactId>mysql-connector-java</artifactId>
          <version>5.1.39</version>
      </dependency>
      <!-- mysql end -->
      <!-- junit -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.12</version>
            <scope>test</scope>
        </dependency>
      <!-- junit end -->
      
      <!--mybatis-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.4.1</version>
        </dependency>
        <!--mybatis-spring组合包-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.1</version>
        </dependency>
      <!--mybatis end-->
      
      <!-- 上传文件使用 -->
        <dependency>
            <groupId>commons-fileupload</groupId>
            <artifactId>commons-fileupload</artifactId>
            <version>1.3.1</version>
        </dependency>
        <dependency>
            <groupId>commons-io</groupId>
            <artifactId>commons-io</artifactId>
            <version>2.4</version>
        </dependency>
        <!-- 上传文件使用end-->
        
        <!-- 加载json -->
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-core</artifactId>
            <version>2.8.0</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-annotations</artifactId>
            <version>2.8.0</version>
        </dependency>
        <dependency>
            <groupId>org.codehaus.jackson</groupId>
            <artifactId>jackson-core-asl</artifactId>
            <version>1.9.13</version>
        </dependency>
        <dependency>
            <groupId>org.codehaus.jackson</groupId>
            <artifactId>jackson-mapper-lgpl</artifactId>
            <version>1.9.13</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.jackson.core</groupId>
            <artifactId>jackson-databind</artifactId>
            <version>2.8.0</version>
        </dependency>
        <!-- 加载json end -->
			
			<!-- validator 数据验证-->
        <!--jsr 303-->
        <dependency>
            <groupId>javax.validation</groupId>
            <artifactId>validation-api</artifactId>
            <version>1.1.0.Final</version>
        </dependency>
        <!-- hibernate validator-->
        <dependency>
            <groupId>org.hibernate</groupId>
            <artifactId>hibernate-validator</artifactId>
            <version>5.2.0.Final</version>
        </dependency>
        <!-- validator 数据验证end-->
        <!-- 日志 -->
        <dependency>
            <groupId>org.jboss.logging</groupId>
            <artifactId>jboss-logging</artifactId>
            <version>3.3.0.Final</version>
        </dependency>
        <!-- 日志end-->
			
			
      <!-- jstl -->
        <!--  HttpServletRequest http请求依赖 -->
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <!--  只在编译和测试阶段运行,因为容器中自带了这个jar包 jetty中自带  -->
            <!--<scope>provided</scope>-->
        </dependency>
        <!--  HttpServletRequest http请求依赖 end -->

        <!--  web el表达式 -->
        <dependency>
            <groupId>javax.servlet.jsp</groupId>
            <artifactId>jsp-api</artifactId>
            <version>2.1</version>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>jstl</artifactId>
            <version>1.2</version>
        </dependency>
        <!--  web el表达式 end -->
        
        <!-- 添加taglibs标签包 -->
        <dependency>
            <groupId>taglibs</groupId>
            <artifactId>standard</artifactId>
            <version>1.1.2</version>
        </dependency>
        <!-- 添加taglibs标签包end -->
        
        <!-- jstl end-->
   
      <!-- slf4j日志包 -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>${log4j.version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-api</artifactId>
            <version>${slf4j.version}</version>
        </dependency>
        <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
            <version>${slf4j.version}</version>
        </dependency>
      <!-- slf4j end -->
      
      <!-- spring start -->
       <!-- spring dbcp连接jar包-->
        <dependency>
            <groupId>commons-dbcp</groupId>
            <artifactId>commons-dbcp</artifactId>
            <version>1.4</version>
        </dependency>
        
        <!-- 自动生成sql-->
				<!-- @Query("update #{#entityName} rule set rule.isEnable=1,rule.lastEnable= NOW() where rule.id= :id")
    @Modifying
    public int enable(@Param("id")int id);
    -->
      <dependency>
	          <groupId>org.springframework.data</groupId>
	          <artifactId>spring-data-jpa</artifactId>
	          <version>1.10.1.RELEASE</version>
	      </dependency>
	      <!-- spring aop包 -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aop</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>aopalliance</groupId>
            <artifactId>aopalliance</artifactId>
            <version>1.0</version>
        </dependency>
        <!-- aop 注解包 -->
        <dependency>  
            <groupId>org.aspectj</groupId>  
            <artifactId>aspectjrt</artifactId>  
            <version>1.6.11</version>  
        </dependency>  
        <dependency>  
            <groupId>org.aspectj</groupId>  
            <artifactId>aspectjweaver</artifactId>  
            <version>1.6.11</version>  
        </dependency>  
        <!-- spring aop包end -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-aspects</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context-support</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-expression</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-orm</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-test</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-tx</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-web</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <!-- spring end -->
  </dependencies>
```

## 插件
```xml
<plugins>
    <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.1</version>
        <configuration>
            <source>1.8</source>
            <target>1.8</target>
        </configuration>
    </plugin>
    <plugin>
        <groupId>org.eclipse.jetty</groupId>
        <artifactId>jetty-maven-plugin</artifactId>
        <version>9.4.5.v20170502</version>
        <configuration>
            <!--开启自动检测配置,默认为0,不改变. 单位为秒 -->
            <scanIntervalSeconds>2</scanIntervalSeconds>
            <httpConnector>
                <!--配置端口号-->
                <port>8080</port>
                <!--空闲超时-->
                <idleTimeout>60000</idleTimeout>
            </httpConnector>
            <!--解决静态文件修改后不刷新的问题-->
            <!--原因是如果NIO被支持的话，Jetty会使用内存映射文件来缓存静态文件，其中包括.js文件。在Windows下面，使用内存映射文件会导致文件被锁定。解决方案是不使用内存映射文件来做缓存。-->
            <!--<webDefaultXml>src/main/resources/webdefault.xml</webDefaultXml>-->
        </configuration>
    </plugin>
    
    <!-- 自动部署到服务器 -->
    <plugin>
        <groupId>org.apache.tomcat.maven</groupId>
        <artifactId>tomcat7-maven-plugin</artifactId>
        <version>2.2</version>
        <configuration>
            <port>8080</port>  <!-- 项目的端口-->
            <path>/taskTwo</path> <!-- web项目的项目名称-->
            <uriEncoding>UTF-8</uriEncoding>
            <!-- 对应的 tomcat manager的接口-->
            <!-- 本地测试 -->
            <!--<url>http://localhost:8080/manager/text</url>-->
            <!-- 远程测试 -->
            <url>http://45.76.214.173:8080/manager/text</url>
            <server>tomcat7</server>  <!-- setting.xml 的server id-->
            <username>tomcat</username> <!-- tomcat-user.xml 的 username -->
                <password>managerstatus</password> <!-- tomcat-user,xml 的 password -->
        </configuration>
    </plugin>
```

## 资源
```xml
<!--没有这个mapping中的xml文件将不会随着编译被迁移到classes中-->
    <resources>
      <resource>
          <directory>src/main/java</directory>
          <includes>
              <include>**/*.xml</include>
              <include>*.properties</include>
          </includes>
          <filtering>true</filtering>
      </resource>
    
      <resource>
          <directory>src/main/resources</directory>
          <includes>
              <include>*.xml</include>
              <include>*.properties</include>
          </includes>
          <filtering>true</filtering>
      </resource>
    </resources>
```

## maven依赖冲突
1. 使用Maven依赖时, 依赖id页下方由其依赖包所需的依赖信息, Compile Dependencies, 可以查看其需要的依赖所需的版本