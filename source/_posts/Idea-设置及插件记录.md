---
title: Idea 设置及插件记录
date: 2020-10-14 09:30:19
tags:
 - Idea

---

#  自动生成注释

## 生成类/接口注释

设置位置: `Setting-Editor-File and Code Templates-Class|Interface`

```java
/**
*
* ${description}
*
* @author caleb_liu@enable-ets.com
*
* @create ${DATE}
**/
```

## 添加类/接口注释模板

设置位置: `Setting-Editor-Live Templates`, 在 `Java` 中添加一个模板或新建一个模板组, 注意不要开头不要写 `/`, 会使 `idea` 内置方法失效, 使用方法: 在类或方法上使用 `/` + 触发关键字

```
**
*
* $description$
*
* @author xxx
* @since $date$ 
**/
```

![image-20201014110718735](Idea-%E8%AE%BE%E7%BD%AE%E5%8F%8A%E6%8F%92%E4%BB%B6%E8%AE%B0%E5%BD%95/image-20201014110718735.png)

![image-20201014110554677](Idea-%E8%AE%BE%E7%BD%AE%E5%8F%8A%E6%8F%92%E4%BB%B6%E8%AE%B0%E5%BD%95/image-20201014110554677.png)



## 添加方法注释模板

```java
**
 * $description$
 * @date $date$ $time$
 * @since caleb_liu@enable-ets.com
$params$
$return$
$throws$
 */
```

`params` 脚本 :

```groovy
groovyScript("if(\"${_1}\".length() == 2) {return '';} else {def result=''; def params=\"${_1}\".replaceAll('[\\\\[|\\\\]|\\\\s]', '').split(',').toList();for(i = 0; i < params.size(); i++) {if(i<(params.size()-1)){result+=' * @param ' + params[i] + ' : ' + '\\n'}else{result+=' * @param ' + params[i] + ' : '}}; return result;}", methodParameters()); 
```

`return` 脚本:

```groovy
groovyScript("def returnType = \"${_1}\"; def result = ' * @return ' + returnType; return result;", methodReturnType());
```

![image-20201014110026383](Idea-%E8%AE%BE%E7%BD%AE%E5%8F%8A%E6%8F%92%E4%BB%B6%E8%AE%B0%E5%BD%95/image-20201014110026383.png)

![image-20201014110035392](Idea-%E8%AE%BE%E7%BD%AE%E5%8F%8A%E6%8F%92%E4%BB%B6%E8%AE%B0%E5%BD%95/image-20201014110035392.png)

# 编码设置

设置位置: `Setting-Editor-File Encodings`   全部勾选 `UTF-8` . 

![image-20201014093545379](Idea-%E8%AE%BE%E7%BD%AE%E5%8F%8A%E6%8F%92%E4%BB%B6%E8%AE%B0%E5%BD%95/image-20201014093545379.png)

# 忽略文件

设置位置: `Setting-Editor-File Types-Ignore Files and Folders` , 添加 `".*; *.iml"`

 # 插件

服务器管理工具,  自带`arthas` 诊断工具: `Alibaba Cloud Toolkit` 

`Mybatis` 跳转插件: `Free Mybatis plugin``

`Mybatis`  sql 生成插件: `MyBatis Log Plugin` 

控制台日志过滤插件: `Grep Console`

热部署套装: `JRebel and XRebel for IntelliJ` 和 `JRebel MybatisPlus`, 激活地址: `http://120.78.90.245:8081/06219f8c-42a9-4d40-b809-048ed91f87bf`

`Lombok` 支持 : `Lombok` 

`Maven` 扩展: `Maven Helper

接口跳转插件: `RestfulTool`

翻译: `Translation`

![image-20201014112306378](Idea-%E8%AE%BE%E7%BD%AE%E5%8F%8A%E6%8F%92%E4%BB%B6%E8%AE%B0%E5%BD%95/image-20201014112306378.png)



