---
title: Idea 初始化配置
date: 2019-03-22 12:21:56
tags:
 - Idea
 - 设置
categories:
 - Idea
---

# 编码
- Setting-Editor-File Encodings- UTF-8
![image](Idea%20%E5%88%9D%E5%A7%8B%E5%8C%96%E9%85%8D%E7%BD%AE/17573)


# 自动生成注释
## 生成类/接口注释
- Setting-Editor-File and Code Templates-Class|Interface
```idea
/**
*
* ${Todo}
*
* @author wb
*
* @create ${DATE}
**/
```
## 生成方法注释
- Setting-Editor-Live Templates
```
**
 * $TODO$
$params$
 * @return $return$
 * @throws
 * @Date $date$ $time$
 * @Author wb
 */
```
主要是生成参数的脚本:
```
groovyScript("def result=''; def params=\"${_1}\".replaceAll('[\\\\[|\\\\]|\\\\s]', '').split(',').toList(); for(i = 0; i < params.size(); i++) {result+=' * param ' + params[i] + ((i < params.size() - 1) ? '\\r\\n    ' : '')}; return result", methodParameters())
```