---
title: Linux Mycat 安装
date: 2020-09-28 11:13:46
tags: 
 - Linux
 - Mycat
 - _mycat_op_time
---

> 最近有个数据迁移需求, 量比较大, 搞个新环境测试下~ 
>
> 以下内容参考至官方文档: [https://github.com/MyCATApache/Mycat-Server/wiki](https://github.com/MyCATApache/Mycat-Server/wiki)

<!--more-->

# 下载 Mycat

官网下载后解压即可.

## 目录说明

```log
--bin		启动目录
--conf		配置文件存放配置文件
--lib		MyCAT自身的jar包或依赖的jar包的存放目录。
--logs		MyCAT日志的存放目录。日志存放在logs/log中，每天一个文件
```

# 运行

Linux 相关脚本:

```bash
./mycat start 启动

./mycat stop 停止

./mycat console 前台运行

./mycat install 添加到系统自动启动（暂未实现）

./mycat remove 取消随系统自动启动（暂未实现）

./mycat restart 重启服务

./mycat pause 暂停

./mycat status 查看启动状态
```

# 配置 Mycat

`Mycat` 最重要的3大配置文件：![img](Linux-Mycat-%E5%AE%89%E8%A3%85/687474703a2f2f736f6e677769652e636f6d2f61747461636865642f696d6167652f32303136303230352f32303136303230353136343535385f3135342e706e67)

## 服务配置 `server.xml`

`system`  参数是所有的 `mycat` 参数配置，比如添加解析器：`defaultSqlParser`，其他类推  `user ` 是用户参数。

添加两个 `mycat` 逻辑库：`user`  和 `pay`.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mycat:server SYSTEM "server.dtd">
<mycat:server xmlns:mycat="http://io.mycat/">
	<system>
		<property name="defaultSqlParser">druidparser</property>
		<property name="mutiNodeLimitType">1</property>
		<property name="serverPort">3309</property>
		<property name="managerPort">9066</property>
		<property name="processors">16</property>
		<property name="processorExecutor">16</property>
		<property name="useOffHeapForMerge">0</property>
	</system>
	<!-- 任意设置登陆 mycat 的用户名,密码,数据库  -->
	<user name="mycat">
		<property name="password">xxx</property>
		<property name="schemas">xxx_schemas</property>
	</user>
</mycat:server>
```

## 逻辑库配置 `schema.xml`

```xml
<?xml version="1.0"?>
<!DOCTYPE mycat:schema SYSTEM "schema.dtd">
<mycat:schema xmlns:mycat="http://io.mycat/">

	<!-- 设置表的存储方式.schema name="TESTDB" 与 server.xml中的 TESTDB 设置一致  -->
	<schema name="xxx_schemas" checkSQLschema="false" sqlMaxLimit="2000">
		<!-- 全局表 dataNode 都会写一份 -->
		<table name="t_as_subject" primaryKey="id" dataNode="node_0$1-4" type="global"/>	
		
        <!-- rule 为分库规则, 这里使用一致性HASH分库, childTable 数据会与父级分到同一个库中 -->
		<table name="t_as_question" primaryKey="question_id" dataNode="node_0$1-4" rule="sharding-by-murmur-question_id">
			<childTable name="t_as_question_option" primaryKey="question_option_id" joinKey="question_id" parentKey="question_id"/>
		</table>
		<table name="t_as_option" primaryKey="option_id" dataNode="node_0$1-4" rule="sharding-by-murmur-option_id"/>
	</schema>

	<!-- 设置dataNode 对应的数据库,及 mycat 连接的地址dataHost -->
	<dataNode name="node_01" dataHost="resource-cluster" database="question_storage_00" />
	<dataNode name="node_02" dataHost="resource-cluster" database="question_storage_01" /> 
	<dataNode name="node_03" dataHost="resource-cluster" database="question_storage_02" />
	<dataNode name="node_04" dataHost="resource-cluster" database="question_storage_03" />
	
	<dataHost name="resource-cluster" maxCon="1000" minCon="50" balance="0" writeType="0" dbType="mysql" dbDriver="native">
	
	<!--心跳检测 -->		
	<heartbeat>select user()</heartbeat>		
	
	<!-- can have multi write hosts -->
	<writeHost host="hostM1" url="127.0.0.1:3306" user="root" password="test123"/>
	</dataHost>
</mycat:schema>
```

## 分库规则 `rule.xml`

定义 `schema.xml` `table` 标签绑定的分库规则:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- - - Licensed under the Apache License, Version 2.0 (the "License"); 
	- you may not use this file except in compliance with the License. - You 
	may obtain a copy of the License at - - http://www.apache.org/licenses/LICENSE-2.0 
	- - Unless required by applicable law or agreed to in writing, software - 
	distributed under the License is distributed on an "AS IS" BASIS, - WITHOUT 
	WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied. - See the 
	License for the specific language governing permissions and - limitations 
	under the License. -->
<!DOCTYPE mycat:rule SYSTEM "rule.dtd">
<mycat:rule xmlns:mycat="http://io.mycat/">
	<tableRule name="sharding-by-murmur-question_id">
		<rule>
		  <columns>question_id</columns>
		  <algorithm>murmur</algorithm>
		</rule>
	</tableRule>
	<tableRule name="sharding-by-murmur-option_id">
		<rule>
		  <columns>option_id</columns>
		  <algorithm>murmur</algorithm>
		</rule>
	</tableRule>
	<function name="murmur" class="io.mycat.route.function.PartitionByMurmurHash">
		<property name="seed">0</property><!-- 默认是0 -->
        <property name="count">4</property><!-- 要分片的数据库节点数量，必须指定，否则没法分片 -->
        <property name="virtualBucketTimes">160</property><!-- 一个实际的数据库节点被映射为这么多虚拟节点，默认是160倍，也就是虚拟节点数是物理节点数的160倍 -->
        <!-- <property name="weightMapFile">weightMapFile</property> 节点的权重，没有指定权重的节点默认是1。以properties文件的格式填写，以从0开始到count-1的整数值也就是节点索引为key，以节点权重值为值。所有权重值必须是正整数，否则以1>代替 -->
        <property name="bucketMapPath">/home/icampus3.0/mycat/bucketMapPath</property>    
        <!--    用于测试时观察各物理节点与虚拟节点的分布情况，如果指定了这个属性，会把虚拟节点的murmur hash值与物理节点的映射按行输出到这个文件，没有默认值，如果不指定，就不会输出任何东西 -->
	</function>
</mycat:rule>
```

# Mycat 报错

## ERROR: No such file or directory (2)

错误信息：

```bash
ERROR  | wrapper  | 2020/09/28 14:27:27 | JVM exited while loading the application.
STATUS | wrapper  | 2020/09/28 14:27:31 | Launching a JVM...
ERROR  | wrapper  | 2020/09/28 14:27:31 | Unable to start JVM: No such file or directory (2)
ERROR  | wrapper  | 2020/09/28 14:27:31 | JVM exited while loading the application.
STATUS | wrapper  | 2020/09/28 14:27:36 | Launching a JVM...
ERROR  | wrapper  | 2020/09/28 14:27:36 | Unable to start JVM: No such file or directory (2)
ERROR  | wrapper  | 2020/09/28 14:27:36 | JVM exited while loading the application.
FATAL  | wrapper  | 2020/09/28 14:27:36 | There were 5 failed launches in a row, each lasting less than 300 seconds.  Giving up.
FATAL  | wrapper  | 2020/09/28 14:27:36 |   There may be a configuration problem: please check the logs.
STATUS | wrapper  | 2020/09/28 14:27:36 | <-- Wrapper Stopped
```

原因可能是JVM参数没有配置或者配置错误,  查看  `wrapper.conf`  中 `wrapper.java.command` 路径是否正确:

```conf
wrapper.java.command=/home/icampus3.0/jdk_1.8.0_212/bin/java
```

## ERROR: 元素类型为 "mycat:rule" 的内容必须匹配 "(tableRule*,function*)"

错误信息:

```
INFO   | jvm 1    | 2020/09/28 14:36:45 | WrapperSimpleApp: Encountered an error running main: java.lang.ExceptionInInitializerError
INFO   | jvm 1    | 2020/09/28 14:36:45 | java.lang.ExceptionInInitializerError
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at io.mycat.MycatStartup.main(MycatStartup.java:53)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at java.lang.reflect.Method.invoke(Method.java:498)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at org.tanukisoftware.wrapper.WrapperSimpleApp.run(WrapperSimpleApp.java:240)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at java.lang.Thread.run(Thread.java:748)
INFO   | jvm 1    | 2020/09/28 14:36:45 | Caused by: io.mycat.config.util.ConfigException: org.xml.sax.SAXParseException; lineNumber: 26; columnNumber: 14; 元素类型为 "mycat:rule" 的内容必须匹配 "(tableRule*,function*)"。
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at io.mycat.config.loader.xml.XMLRuleLoader.load(XMLRuleLoader.java:95)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at io.mycat.config.loader.xml.XMLRuleLoader.<init>(XMLRuleLoader.java:64)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at io.mycat.config.loader.xml.XMLSchemaLoader.<init>(XMLSchemaLoader.java:74)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at io.mycat.config.loader.xml.XMLSchemaLoader.<init>(XMLSchemaLoader.java:87)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at io.mycat.config.ConfigInitializer.<init>(ConfigInitializer.java:74)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at io.mycat.config.MycatConfig.<init>(MycatConfig.java:72)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at io.mycat.MycatServer.<init>(MycatServer.java:144)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at io.mycat.MycatServer.<clinit>(MycatServer.java:96)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       ... 7 more
INFO   | jvm 1    | 2020/09/28 14:36:45 | Caused by: org.xml.sax.SAXParseException; lineNumber: 26; columnNumber: 14; 元素类型为 "mycat:rule" 的内容必须匹配 "(tableRule*,function*)"。
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at com.sun.org.apache.xerces.internal.util.ErrorHandlerWrapper.createSAXParseException(ErrorHandlerWrapper.java:203)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at com.sun.org.apache.xerces.internal.util.ErrorHandlerWrapper.error(ErrorHandlerWrapper.java:134)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at com.sun.org.apache.xerces.internal.impl.XMLErrorReporter.reportError(XMLErrorReporter.java:396)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at com.sun.org.apache.xerces.internal.impl.XMLErrorReporter.reportError(XMLErrorReporter.java:327)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at com.sun.org.apache.xerces.internal.impl.XMLErrorReporter.reportError(XMLErrorReporter.java:284)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at com.sun.org.apache.xerces.internal.impl.dtd.XMLDTDValidator.handleEndElement(XMLDTDValidator.java:1994)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at com.sun.org.apache.xerces.internal.impl.dtd.XMLDTDValidator.endElement(XMLDTDValidator.java:879)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at com.sun.org.apache.xerces.internal.impl.XMLDocumentFragmentScannerImpl.scanEndElement(XMLDocumentFragmentScannerImpl.java:1782)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at com.sun.org.apache.xerces.internal.impl.XMLDocumentFragmentScannerImpl$FragmentContentDriver.next(XMLDocumentFragmentScannerImpl.java:2967)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at com.sun.org.apache.xerces.internal.impl.XMLDocumentScannerImpl.next(XMLDocumentScannerImpl.java:602)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at com.sun.org.apache.xerces.internal.impl.XMLDocumentFragmentScannerImpl.scanDocument(XMLDocumentFragmentScannerImpl.java:505)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at com.sun.org.apache.xerces.internal.parsers.XML11Configuration.parse(XML11Configuration.java:842)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at com.sun.org.apache.xerces.internal.parsers.XML11Configuration.parse(XML11Configuration.java:771)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at com.sun.org.apache.xerces.internal.parsers.XMLParser.parse(XMLParser.java:141)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at com.sun.org.apache.xerces.internal.parsers.DOMParser.parse(DOMParser.java:243)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at com.sun.org.apache.xerces.internal.jaxp.DocumentBuilderImpl.parse(DocumentBuilderImpl.java:339)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at javax.xml.parsers.DocumentBuilder.parse(DocumentBuilder.java:121)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at io.mycat.config.util.ConfigUtil.getDocument(ConfigUtil.java:115)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       at io.mycat.config.loader.xml.XMLRuleLoader.load(XMLRuleLoader.java:86)
INFO   | jvm 1    | 2020/09/28 14:36:45 |       ... 14 more
STATUS | wrapper  | 2020/09/28 14:36:47 | <-- Wrapper Stopped
```

这个错误原因是 `rule.xml` 中 `function` 配置内容不能在 `tableRule` 配置之前, 调换下顺序即可

```xml
<tableRule name="sharding-by-murmur">
    <rule>
        <columns>id</columns>
        <algorithm>murmur</algorithm>
    </rule>
</tableRule>
<function name="murmur" class="io.mycat.route.function.PartitionByMurmurHash">
    <property name="seed">0</property><!-- 默认是0 -->
    <property name="count">4</property><!-- 要分片的数据库节点数量，必须指定，否则没法分片 -->
    <property name="virtualBucketTimes">160</property><!-- 一个实际的数据库节点被映射为这么多虚拟节点，默认是160倍，也就是虚拟节点数是物理节点数的160倍 -->
    <!-- <property name="weightMapFile">weightMapFile</property> 节点的权重，没有指定权重的节点默认是1。以properties文件的格式填写，以从0开始到count-1的整数值也就是节点索引为key，以节点权重值为值。所有权重值必须是正整数，否则以1>代替 -->
    <property name="bucketMapPath">/home/mycat/bucketMapPath</property>    
    <!--    用于测试时观察各物理节点与虚拟节点的分布情况，如果指定了这个属性，会把虚拟节点的murmur hash值与物理节点的映射按行输出到这个文件，没有默认值，如果不指定，就不会输出任何东西 -->
</function>
```

## Navicat 打开表报错: `find no route`

错误信息:

 ![image-20200928144809077](Linux-Mycat-%E5%AE%89%E8%A3%85/image-20200928144809077.png)

修改 `schema.xml` 的 `checkSQLschema=“false”` , 改为 `true` 即可. 

当该值为 `true` 时，例如我们执行语句 `select * from TESTDB.company`. `mycat` 会把语句修改为 `select * from company ` 去掉 `TESTDB`。

```xml
<schema name="db_store" checkSQLschema="true" sqlMaxLimit="100">
```

## SQL 排序报错: `all columns in order by clause should be in the selected column list!xxx`

这是 `mycat` 的一个 `bug` ，无法识别sql中的 \` 符号, 去掉  `sql` 中的 \` 号即可.

详情: [mycat执行sql报 all columns in group by clause should be in the selected column list.!`logis_id`错误](https://blog.csdn.net/xiaofo258/article/details/95065562)

源码: 

![img](Linux-Mycat-%E5%AE%89%E8%A3%85/20190708155402355.png)

## Mycat 插入报错:  Unknown column '_mycat_op_time' in 'field list'

这个是由参数 `useGlobleTableCheck` 控制的全局表一致性检测，原理通过在全局表增加 `_MYCAT_OP_TIME` 字段来进行一致性检测，类型为 `bigint`，`create`语句通过 `mycat` 执行会自动加上这个字段，其他情况请自己手工添加。 

在创建全局表类型的表时添加这个字段: 

```mysql
`_mycat_op_time` bigint(20) DEFAULT NULL
```

