---
title: 
date: 2019-08-30 13:22:51
tags:
 - SpringBoot
 - 经验
categories:
 - SpringBoot
---

[TOC]

> 最近刚使用上 opencv 做图像处理, 部署后发现每次调完业务代码 RES 都会上涨近 300M, 测试环境测了两天, RES 已经到 11G 了, 开始以为是内存泄露, 各种释放对象, 然而没卵用...

# 一. 开始排查
一开始以为是内存泄露了, 各种 google 查看怎么排查, 
学到了不少, 之前都没怎么看过..

查看内存占用比较高的前20个 (不知道为啥只有17个), 而且最高的是个 `[C` 对象, 看了一下没看出来啥.
```
jmap -histo 32382 | head -20
```
![](https://ws1.sinaimg.cn/large/b08fb805gy1g3p5j0kfizj20j50970t3.jpg)

导出堆内存, 可以下载下来用 JDK 自带的工具 jvisualvm 查看, 也可以直接在服务器上起服务查看, 占用最高的是 char[], 还有一个 HashMap, 代码中查了一圈, 没发现问题..
```
# 导出堆内存
jmap -dump:format=b,file=dump.bin 32382
# 起服务, 直接web访问 ip:4000
jhat -port 4000 dump.bin
```
![](https://ws1.sinaimg.cn/large/b08fb805gy1g3p5r77u6kj214108xq3j.jpg)

# 二. 确认问题
最后转念一想, 会不会不是堆的问题, 做了三次测试, 每次调用业务接口内存都会上涨. 但是堆内存占用都没有什么变化, 这是当时的三次堆内存信息:
```
jmap -heap 32382 
```
![](https://ws1.sinaimg.cn/large/b08fb805gy1g3p5w91b38j20fp0b23yr.jpg)
![](https://ws1.sinaimg.cn/large/b08fb805gy1g3p5x947vpj20ec0b9glu.jpg)
![](https://ws1.sinaimg.cn/large/b08fb805gy1g3p5xweipdj20fb0cjwer.jpg)

查看堆使用情况, 发现只有 850M 左右, 基本确定了不是堆的问题. 
```
jmap -histo:live 32382
```
![](https://ws1.sinaimg.cn/large/b08fb805gy1g3p5zg3g3gj20mk01ma9x.jpg)

# 三. 解决方法
想着 opencv 使用 JNI 方式做的, 会不会是 Native Memory, 继续google.. 终于找到了一片文章: [Java堆外内存增长问题排查Case](https://coldwalker.com/2018/08//troubleshooter_native_memory_increase/), 用里面介绍的查看物理内存分配, 看到了大量的 anon 内存块, 不过我的都是1024的, 跟文章中有所不同. 
```
pmap -d 32382
```
![](https://ws1.sinaimg.cn/large/b08fb805gy1g3p63ts1akj20hk06rdfx.jpg)

文章中还提供了一个链接: [当Java虚拟机遇上Linux Arena内存池](https://yq.aliyun.com/articles/227924), 第二个例子跟我差不多, 都是 1024 的, 原因文章中也有介绍 : 
![](https://ws1.sinaimg.cn/large/b08fb805gy1g3p68vtymij20ld0codh4.jpg)

查看 glibc 版本: 
```
/lib64/libc.so.6
```
![](https://ws1.sinaimg.cn/large/b08fb805gy1g3p6i5j6hpj20g908874l.jpg)

因为要部署到线上环境耗时耗力, 所以我没有更换 `tcmalloc` , 在启动脚本中加了一下环境变量: 
```
export MALLOC_ARENA_MAX=4
```
测试了一下, RES 始终稳定在 1.3g 左右, 已经很满意了. `export MALLOC_ARENA_MAX` 设置为 1 时内存在 1g 左右, 但文章中说可能对性能有损耗, 所以我还是设置为 4 了, 持续观察~

看着不多, 但是这一块相当不熟悉, 所以花了很久才找到解决方法, 继续学习啊~