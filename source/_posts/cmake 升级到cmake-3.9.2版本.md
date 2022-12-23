---
title: cmake 升级到cmake-3.9.2版本
date: 2019-07-29 13:24:48
tags:
 - cmake
 - 升级
categories:
 - cmake
---

# 一. 查看当前版本
1. cmake -version
    ```
    [root@host ~]# cmake -version
    cmake version 2.8.12.2
    ```

# 二. 安装新版本cmake
## 1. 在官网下载需要的新版本`cmake`源码
 - ftp地址: [https://cmake.org/files/](https://cmake.org/files/)
 - 3.9.2 地址: [https://cmake.org/files/v3.9/cmake-3.9.2.tar.gz](https://cmake.org/files/v3.9/cmake-3.9.2.tar.gz)

## 2. 下载编译
1. 下载解压
    ```
    wget https://cmake.org/files/v3.9/cmake-3.9.2.tar.gz
    tar -xvf cmake-3.9.2.tar.gz
    ```
2. 编译, 花了我二十分钟左右..
    ```
    cd cmake-3.9.2
    ./configure
    sudo make && make install
    ```

## 3. 注意事项
1. 安装完后，执行`cmake --version`会报如下错误
    ```
    [root@host cmake-3.9.2]# cmake -version
    CMake Error: Could not find CMAKE_ROOT !!!
    CMake has most likely not been installed correctly.
    Modules directory not found in
    /usr/local/bin
    Segmentation fault
    ```

- 解决方法, 暂时不知道啥意思.. 像是是执行了一下`cmake`下的`hash`命令, 刷新了`cmake`的环境变量
    ```
    [root@host cmake-3.9.2]# hash -r
    [root@host cmake-3.9.2]# cmake --version
    cmake version 3.9.2
    
    CMake suite maintained and supported by Kitware (kitware.com/cmake).
    ```