---
title: CentOS 6 GCC 编译升级至5.1.0
date: 2021-01-07 13:14:50
tags:
 - CentOS6
 - GCC
 - 升级
categories:
 - Linux
---

由于 CentOS 6 停止更新支持了, 官方都将yum软件源关闭了. 原来的安装方式已失效. 只能手动编译了.

1. 下载GCC 5.1.0 源码包: `wget http://mirrors.ustc.edu.cn/gnu/gcc/gcc-5.1.0/gcc-5.1.0.tar.bz2`

2. 解压: `tar xvf gcc-5.1.0.tar.bz2`

3. 安装gcc需要下载诸如gmp、mpfr、mpc等依赖文件，执行`download_prerequisites`将会自动下载这些软件并解压到当前目录: `./contrib/download_prerequisites`

4. 新建一个build目录, 执行mark前的configura
    ```
    mkdir build
    cd build 
    ../configure --prefix=/usr/local/gcc-5.1.0 --disable-multilib --enable-languages=c,c++,java
    ```
5. 开始编译: `make -j4`

6. 安装 
    ```
    make install
    which gcc # 将原来的gcc改名
    mv /usr/bin/gcc /usr/bin/gcc_bak
    ln  -s /usr/local/gcc-5.1.0/bin/x86_64-unknown-linux-gnu-gcc-5.1.0 /usr/bin/gcc # 创建软连接
    ```

测试: `gcc --version`