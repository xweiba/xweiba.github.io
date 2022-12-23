---
title: docker opencv java
date: 2019-11-13 11:01:47
tags:
 - opencv
 - docker
categories:
 - docker
---

Dockerfile:
```
FROM centos:7.4.1708

MAINTAINER noasking

# 更换阿里源
RUN yum install -y wget
RUN cd /etc/yum.repos.d \
    && mv CentOS-Base.repo CentOS-Base.repo.bak \
    && wget -O CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo \
    && yum clean all \
    && yum makecache

# 必要なパッケージインストール
################################################################################
RUN yum update -y \
        && yum install -y git gcc gcc-c++ autoconf automake cmake \
                          freetype-devel libtool make mercurial nasm \
                          pkgconfig zlib-devel \
                          bzip2-devel hostname \
                          openssl \
                          openssl-devel \
                          wget \
                          which \
                          boost* \
                          ant \
        && yum clean all

# Java 安装
ADD jdk-8u131-linux-x64.tar.gz /

# 配置环境变量
ENV JAVA_HOME /jdk1.8.0_131
ENV JRE_HOME $JAVA_HOME/jre
ENV CLASSPATH .:$JAVA_HOME/lib:$JRE_HOME/lib
ENV PATH $PATH:$JAVA_HOME/bin

# OpenCV 配置
################################################################################
COPY opencv-3.4.0.tar.gz /

RUN cd \
    && mkdir opencv && tar xvzf /opencv-3.4.0.tar.gz -C opencv --strip-components 1 \
    && cd opencv \
    && mkdir build \
    && cd build \
    && cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/opencv/release -D BUILD_TESTS=OFF ..\
    && make -j8 \
    && make install \
    && cp /opencv/release/share/OpenCV/java/libopencv_java340.so /usr/local/lib/ \
    && cd \
    && rm -f /opencv-3.4.0.tar.gz

# Boostのパス設定
ENV BOOST_ROOT /usr/lib64/
ENV Boost_INCLUDE_DIR /usr/include/boost/
# LD_LIBRARY_PATH設定
ENV LD_LIBRARY_PATH $LD_LIBRARY_PATH:/usr/local/lib

# WORK_DIRECTORY設定
WORKDIR /app

# CMD設定(BASH)
CMD ["/bin/bash"]
```

java8 ：[下载地址](https://download.oracle.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jdk-8u131-linux-x64.tar.gz?AuthParam=1568135886_528af9016dad133b0ae80c04ec8286c6)

opencv : [下载地址](https://github.com/opencv/opencv/archive/4.1.1.tar.gz)

docker build -t xiaoweiba1028/opencv-java . 