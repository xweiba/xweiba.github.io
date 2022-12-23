---
title: make 报错记录
date: 2019-11-13 13:20:10
tags:
 - make
 - 经验
 - 错误记录
categories:
 - make
---

1. c++: internal compiler error: Killed (program cc1plus)
   网上说是内存原因, 一般云主机都是`1G`左右内存, 没开启`swap`, 我`make` 时开启了多线程处理, 非常有可能是这个原因
    - 错误提示
    ```
    c++: internal compiler error: Killed (program cc1plus)
    Please submit a full bug report,
    with preprocessed source if appropriate.
    See <http://bugzilla.redhat.com/bugzilla> for instructions.
    make[2]: *** [modules/CMakeFiles/ade.dir/__/3rdparty/ade/ade-0.1.1d/sources/ade/source/passes/communications.cpp.o] Error 4
    make[2]: *** Waiting for unfinished jobs....
    ```
    - 解决方法, 启用`swap`: [linux 创建 swap 交换空间](http://note.youdao.com/noteshare?id=672129707b187ffc8b03813fabe374ec)