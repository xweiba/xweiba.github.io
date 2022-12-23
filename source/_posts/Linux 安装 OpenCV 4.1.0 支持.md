---
title: Linux 安装 OpenCV 4.1.0 支持
date: 2019-11-15 13:18:12
tags:
 - Linux
 - OpenCV
categories:
 - Linux
---

[TOC]

# OpenCV 简介
OpenCV（Open Source Computer Vision Library）是一个开源的计算机视觉库，它提供了很多函数，这些函数非常高效地实现了计算机视觉算法（最基本的滤波到高级的物体检测皆有涵盖）。

OpenCV 是跨平台的，可以在  Windows、Linux、Mac OS、Android、iOS 等操作系统上运行。

OpenCV 的应用领域非常广泛，包括图像拼接、图像降噪、产品质检、人机交互、人脸识别、动作识别、动作跟踪、无人驾驶等。

OpenCV 还提供了机器学习模块，你可以使用正态贝叶斯、K最近邻、支持向量机、决策树、随机森林、人工神经网络等机器学习算法。

更多 OpenCV 介绍请参考百度百科：https://baike.baidu.com/item/opencv/10320623

# 编译环境要求
> 本次编译安装基于 `CentOS release 6.5 (Final)`.  建议开始之前查看各个依赖版本是否符合要求, 下列是主要的依赖: 
- GCC 4.8.x or higher - [GCC 升级](#gccup)
- CMake 3.5.x or higher  - [CMake 升级](#cmakeup)
- Python 2.7.x or higher  -  [Python 升级](#pythonup)
- 注意`JDK`版本要与运行的 `jar` 包版本一致! - [设置临时JAVA环境变量](#jdkup)

# 一. 下载 opencv 源码
```
git clone https://github.com/opencv/opencv
```

# 二. 编译及依赖安装
## 1. 安装依赖项目
```
yum install cmake gcc gcc-c++ gtk+-devel gimp-devel gimp-devel-tools gimp-help-browser zlib-devel libtiff-devel libjpeg-devel libpng-devel gstreamer-devel libavc1394-devel libraw1394-devel libdc1394-devel jasper-devel jasper-utils swig python libtool nasm build-essential ant
```
- 其中需要注意的是 `build-essential` 在 `centOS` 通过下面的方式来安装 `yum groupinstall "Development Tools"`

## 2. 配置 `opencv` 编译选项
```
cd opencv
mkdir build && cd build
cmake -DCMAKE_BUILD_TYPE=RELEASE -DBUILD_TESTS=OFF -DBUILD_TIFF=ON -DCMAKE_INSTALL_PREFIX=/usr/local/opencv/4.1.0 ..
    - 注意`-D`后不留空格
    - /usr/local/xxx 为安装目录
    - 最后两点是源码目录
    - -DBUILD_TESTS=OFF 关闭测试, 编译时比较耗时间
    - -DBUILD_TIFF=ON 报 error: ‘tmsize_t’ 错误时添加, centOS6 特有.
    - -DWITH_V4L=OFF 报 error: ‘V4L2_CID_ISO_SENSITIVITY’ 错误时添加, 在 centOS6 上编译 3.4.3 以上版本才需要加这个.
```
- `cmake` 完成后注意查看报告中的 `To be built: ` 行后有无 `java` 支持, 没有的话排查报告中的排查 `python` 与 `java` 项目是否正常,  `ant` 建议 `1.9.*` 版本, `python` 必须 `2.7` 或以上版本.

## 3. 编译 `opencv`
1. 编译, 需要半小时左右
    ```
    make -j2
    ```
    - -j2 同时使用 2 个线程编译, 可以自己测试用几个线程, 单核服务器开两个刚好把 `cpu` 跑满.
    - 在 `centos6` 上编译, `gcc` 版本得在 `4.8.5` 或以上.
    - 在 `centos6` 上编译 `opencv 3.4.4` 以上版本时会出现 `opencv error: ‘V4L2_CID_ISO_SENSITIVITY’ was not declared in this scope `这类错误，在 `centos6` 暂时上无法解决只能在 `cmake` 时加入 `-D WITH_V4L=OFF` 命令在编译时关掉 `v4l2` 功能或者尝试编译 `3.4.3` 版本,原因见 [issue](https://github.com/opencv/opencv/issues/13335)
2. 安装
    ```
    sudo make install
    ```
3. 依赖配置
    ```linux
    # 复制 `so` 文件, 一定要把 `so` 文件复制过去, 不然 `jar` 找不到依赖, 类似 `windows` 下的 `dll`, 也可以把该目录加到环境变量中
    cd /usr/local/opencv/4.1.0/share/java/opencv4
    cp libopencv_java410.so /usr/lib 
    ```

# 三. 测试运行
## 1. 下载依赖 `jar` 至本地 
- 将 `/usr/local/opencv/4.1.0/share/java/opencv4` 下的 `opencv-410.jar` 下载到本地.

## 2. 将依赖 `jar` 安装至本地 `maven` 仓库:
```
# 已添加到公司私有 Maven 仓库
mvn install:install-file -Dfile=opencv-410.jar -DgroupId=org.bytedeco.javacpp-presets -DartifactId=opencv-linux -Dversion=4.1.0 -Dpackaging=jar
```

## 3. 新建一个测试 `demo` 项目
1. 添加依赖
    ```
    <!-- 添加 opencv 依赖 -->
    <dependency>
        <groupId>org.bytedeco.javacpp-presets</groupId>
        <artifactId>opencv-linux</artifactId>
        <version>4.1.0</version>
    </dependency>
    ```
2. `jaca` 测试代码:
    ```java
    public class OpenCvDemo {
        static { 
            System.loadLibrary(Core.NATIVE_LIBRARY_NAME); 
        }
    
        public static void main(String[] args) {
    
            if ((1==args.length) && (0==args[0].compareTo("--build"))) {
    
                System.out.println(Core.getBuildInformation());
            } else
            if ((1==args.length) && (0==args[0].compareTo("--help"))) {
    
                System.out.println("\t--build\n\t\tprint complete build info");
                System.out.println("\t--help\n\t\tprint this help");
            } else {
    
                System.out.println("Welcome to OpenCV " + Core.VERSION);
            }
        }
    }
    ```
3. 打包时把依赖`jar`加进去
    ```xml
    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-assembly-plugin</artifactId>
                <version>3.1.0</version>
                <configuration>
                    <archive>
                        <manifest>
                            <mainClass>com.xweba.opencv.OpenCvDemo</mainClass>
                        </manifest>
                    </archive>
                    <descriptorRefs>
                        <descriptorRef>jar-with-dependencies</descriptorRef>
                    </descriptorRefs>
                </configuration>
                <executions>
                    <execution>
                        <id>make-assembly</id> <!-- this is used for inheritance merges -->
                        <phase>package</phase> <!-- 指定在打包节点执行jar包合并操作 -->
                        <goals>
                            <goal>single</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
    ```
    
## 4. 将 `demo` 包上传至服务器运行测试:
```
[root@host opencv4]# cd ~/opencv/samples/java/
[root@host java]# java -jar opencv-demo-0.0.1-jar-with-dependencies.jar
Welcome to OpenCV 4.1.0-dev
// --buidl 查看OpenCV环境信息
[root@host java]#java -jar opencv-demo-0.0.1-jar-with-dependencies.jar --build
```

# [注] 可能会遇到的报错及解决方法
## 1. `cmake` 相关
### a. `cmake` 配置命令报错
- 错误信息:
    ```
    CMake Error: The source directory "/root/opencv/build/CMAKE_INSTALL_PREFIX=/usr/local/opencv/4.1.0" does not exist.
    Specify --help for usage, or press the help button on the CMake GUI.
    ```
- 解决方法:
    ```
    把`-D` 后的空格都去掉
    cmake -DCMAKE_BUILD_TYPE=RELEASE -DCMAKE_INSTALL_PREFIX=/usr/local/opencv/4.1.0 ..
    ```
    <a id="cmakeup"></a>
### b. `cmake` 版本过低 
- 错误信息:
    ```
    [root@host build]# cmake -DCMAKE_BUILD_TYPE=RELEASE -DCMAKE_INSTALL_PREFIX=/usr/local/opencv/4.1.0 ..
    CMake Error at CMakeLists.txt:29 (cmake_minimum_required):
    CMake 3.5.1 or higher is required.  You are running version 2.8.12.2
    -- Configuring incomplete, errors occurred!
    ```
- 解决方法升级`cmake` : [cmake 升级到 cmake-3.9.2 版本](http://note.youdao.com/noteshare?id=8d107cf5d4e0b93190359a606e436354&sub=9F24A61F02F94574A9AC23CEA75DD998)
### c. `cmake` 时 `ant` 启动报错
- 错误信息:
    ```
    Exception in thread "main" java.lang.NoClassDefFoundError: org/apache/tools/ant/launch/Launcher
    ```
- 解决方法:
    ```
    #添加 ant 环境变量
    vim /etc/profile
    export ANT_HOME=/usr/share/ant/
    export CLASSPATH=$CLASSPATH:$CLASSPATH:$ANT_HOME/lib
    source /etc/profile
    ```
### d. `cmake` 时卡在 `download ippicv_2019_lnx_intel64_general_20180723.tgz`
由于它资源是在`github` 上的, 可能有点慢, 可以自行去  [github](https://github.com/opencv/opencv_3rdparty/tree/ippicv/master_20180723/ippicv) 上下载至本地, 然后:
```
vim ~/opencv/opencv-master/3rdparty/ippicv/ippicv.cmake 
"file:///root/opencv/" #47行修改为本地文件, 再次 cmake
```

## 2. `make` 相关
<a id="gccup"></a>
### a. `gcc` 无法识别命令行
- 错误信息 : 
    ```
    cc1plus: 警告：命令行选项“-Wmissing-prototypes”对 Ada/C/ObjC 是有效的，但对 C++ 无效
    cc1plus: 警告：命令行选项“-Wstrict-prototypes”对 Ada/C/ObjC 是有效的，但对 C++ 无效
    cc1: 警告：无法识别的命令行选项“-Wno-implicit-fallthrough”
    cc1: 警告：无法识别的命令行选项“-Wno-maybe-uninitialized”
    cc1: 警告：无法识别的命令行选项“-Wno-unnamed-type-template-args”
    cc1: 警告：无法识别的命令行选项“-Wno-delete-non-virtual-dtor”
    ```
- 解决办法 : gcc 版本过低, 至少 4.8.x 或以上: 
    - [GCC 升级至6.X](https://www.vpser.net/manage/centos-6-upgrade-gcc.html)
- 升级 `gcc` 报错:
    ```
    Error: Protected multilib versions: libgcc-4.8.2-8.el6.x86_64 != libgcc-4.4.7-23.el6.i686
     You could try using --skip-broken to work around the problem
    ** Found 34 pre-existing rpmdb problem(s), 'yum check' output follows:
    ```
    原因是多个库不能共存
- 解决方法:  
    - 来源: https://blog.csdn.net/chenxin2tj/article/details/79950904
    ```
    yum install gcc gcc-g++ -y --setopt=protected_multilib=false
    ```
### b. 编译 `cpp` 时错误
- error: ‘Z_FIXED’
    - 解决方法: 在 `grfmt_png.cpp` 中添加 `#define Z_FIXED 4` . 来源: [Python3 opencv3编译](https://anribras.github.io/tech/2018/02/09/python3-opencv3%E7%BC%96%E8%AF%91/)
- error: ‘tmsize_t’
    - 解决方法一: 在 `cmake` 时添加 `-DBUILD_TIFF=ON`. 来源: [Python3 opencv3编译](https://anribras.github.io/tech/2018/02/09/python3-opencv3%E7%BC%96%E8%AF%91/)
    - 解决方法二: `yum remove libtiff-dev` 把libtiff-dev依赖卸载，让opencv使用3rdparty内的libtiff去编译, 来源: [CentOS 6.9 安装OpenCV 3.4.4](https://www.twblogs.net/a/5c12702ebd9eee5e4183d1bd/zh-cn)
- error: ‘V4L2_CID_ISO_SENSITIVITY’
    - 解决方法: 在 `cmake` 时添加 `-DWITH_V4L=OFF`. 来源: [CentOS 6.9 安装OpenCV 3.4.4](https://www.twblogs.net/a/5c12702ebd9eee5e4183d1bd/zh-cn)

### c. `make` 编译时内存不够
- 错误信息:
    ```
    c++: internal compiler error: Killed (program cc1plus)
    Please submit a full bug report,
    with preprocessed source if appropriate.
    See <http://bugzilla.redhat.com/bugzilla> for instructions.
    make[2]: *** [modules/CMakeFiles/ade.dir/__/3rdparty/ade/ade-0.1.1d/sources/ade/source/passes/communications.cpp.o] Error 4
    make[2]: *** Waiting for unfinished jobs....
    ```
- 解决方法开启 `swap 交换空间` :  [linux 创建 swap 交换空间](http://note.youdao.com/noteshare?id=672129707b187ffc8b03813fabe374ec)
  
## 3. `make` 后
### a. 无 `java` 模块
- 检查 `cmake` 结果中 `python` 与 `java` 项是否正常
    ```
    --   Python (for build):            NO
    --
    --   Java:
    --     ant:                         /usr/share/ant/bin/ant (ver 1.9.13)
    --     JNI:                         /usr/local/jdk1.8/jdk1.8.0_91/include /usr/local/jdk1.8/jdk1.8.0_91/include/linux /usr/local/jdk1.8/jdk1.8.0_91/include
    --     Java wrappers:               NO
    --     Java tests:                  NO
    ```
- 检查 `python` 版本, 需要 `python2.7` 及以上版本 <a id="pythonup"></a>
    - 升级 `python` : [Linux Python 2.6 升级至 2.7](http://note.youdao.com/noteshare?id=c0462d64143941abce1a425d50dba82a)

<a id="jdkup"></a>
## 4. 执行出错
`OpenCV`的编译 `JDK` 版本与运行 `jar` 包的编译版本不一致会导致执行出错， 错误日志：
```log
java.lang.UnsatisfiedLinkError: org.opencv.imgcodecs.Imgcodecs.imread_1(Ljava/lang/String;)J
```

```log
Exception in thread "main" java.lang.UnsupportedClassVersionError: org/opencv/core/Core : Unsupported major.minor version 52.0
```

此时需要更改环境变量后重新编译 `OpenCV`.

将下列环境变量写入 `/etc/profile` 永久生效，直接在 `shell` 中执行临时生效 , 操作完后重新 `make` : 
```shell
JAVA_HOME=/home/icampus3.0/jdk8
JAVA_BINDIR=$JAVA_HOME/bin
JAVA_ROOT=$JAVA_HOME
JRE_ROOT=$JAVA_HOME/jre
CLASSPATH=$CLASSPATH:$JAVA_HOME/lib/rt.jar:$JAVA_HOME/lib/tools.jar
PATH=$JAVA_BINDIR:$PATH
export PATH JAVA_HOME JAVA_BINDIR JAVA_ROOT CLASSPATH
```

ps: `windows` 端启动配置指定 `dll` 位置 `-Djava.library.path=D:\OpenOCR\4.1.0\build\java\x64` 或将该路径加入环境变量

