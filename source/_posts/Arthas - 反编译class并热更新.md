---
title: Arthas - 反编译class并热更新
date: 2021-06-30 12:16:42
tags:
 - Arthas
 - debug
 - 调试
categories:
 - Arthas
---



# 1.反编译

jad --source-only com.enableets.edu.paper.framework.service.TransformPaperService > /root/TransformPaperService.java
注意 重定向的写入会有多余信息, 需手动删除


# 2.查找classLoaderHash
sc -d com.enableets.edu.paper.framework.service.TransformPaperService | grep classLoaderHash
会获取内存编译所需的 classLoaderHash

# 3.使用内存编译
mc -c 5b2133b1 /root/TransformPaperService.java -d /root/TransformPaperService.class

# 4.热部署(如果改动不大,可直接修改本地源码, 将编译后的class热部署即可)
redefine /root/TransformPaperService.class

# 可能会遇到的报错
- 反编译报错 `Memory compiler error, exception message: Compilation Error`
  经搜索是 `Arthas` 反编译器有bug, 可将class拖出来使用idea编译, 直接使用 `redefine` 热部署即可
- 反编译报错属性值不对, 因为反编译出的class有问题, 将源码拖出来自己慢慢改. 