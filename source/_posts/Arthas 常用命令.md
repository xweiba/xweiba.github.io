---
title: Arthas 常用命令
date: 2022-12-23 12:17:53
tags:
 - Arthas
 - 常用命令
categories:
 - Arthas
---

# Arthas 常用命令
官方文档：[Arthas 命令列表](https://alibaba.github.io/arthas/commands.html)

## 启动 Arthas
```
wget https://alibaba.github.io/arthas/arthas-demo.jar
java -jar arthas-demo.jar

curl -O https://alibaba.github.io/arthas/arthas-boot.jar
java -jar arthas-boot.jar
```
注意退出的时候要 `stop`, 用 `quit` 不会退出.

## 开启日志
默认情况下，该功能是关闭的，如果需要开启，请执行以下命令：
```
$ options save-result true
 NAME         BEFORE-VALUE  AFTER-VALUE
----------------------------------------
save-result  false         true
Affect(row-cnt:1) cost in 3 ms.
```
看到上面的输出，即表示成功开启该功能；

日志文件路径: 结果会异步保存在：{user.home}/logs/arthas-cache/result.log，请定期进行清理，以免占据磁盘空间

输出指定数据: `watch com.enableets.edu.assessment.framework.reportV2.factory.AbstractReportFactory dataSliceQuery '{params,returnObj}' -x 6 > dataSliceQuery.txt`

文件会存储至 `jar` 包目录

## watch
![](Arthas%20%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4/SBPvmZ6NukwCETe.png)
watch 依次参数：接口地址 方法名称 ognl表达式 观察模式(可不需要)
- 打印方法第一个入参：
    ```
    watch com.test.xxx query 'params[0]'
    ```

- 打印入参和返回值
    ```
    watch com.xxx.Service xxxFunction '{params,returnObj}' -x 6
    ```

- 如果入参是对象，打印的是对象地址，可通过参数名`params[0].name`或调用JSON转换成字符串，也可以使用`-x 3(遍历属性深度层次数)`
    ```
    watch com.test.xxx query '@com.alibaba.fastjson.JSON@toJSON({params[0]})[0]'
    ```
    ![](Arthas%20%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4/cd1xELTvPwK89BO.png)
    ```
    watch com.test.xxx query '{params[0]}' -b -x 3
    ```
    ![](Arthas%20%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4/BWNucritTZXlLRp.png)

## SC
- 查找 `classLoaderHash` 
    ```
    # 随便找一个实例即可获取 classLoaderHash
    sc -d com.test.xxx.util.BeanUtils | grep classLoaderHash
    ```

## ognl 
可通过 `ognl` 表达式执行某个静态方法
- 修改某个对象的 `debug` 级别
    ```
    ognl -c 5b2133b1 '@com.test.xxx.service.CommCacheDataService@LOGGER.setLevel(@ch.qos.logback.classic.Level@INFO)'
    ```
- 修改全局的 `debug` 级别
    ```
    ognl -c 5b2133b1 '@org.slf4j.LoggerFactory@getLogger("root").setLevel(@ch.qos.logback.classic.Level@INFO)'
    ```