---
title: Anacoda OpenCV 环境搭建
date: 2021-01-13 14:02:47
tags:
 - Anacoda
 - OpenCV
 - Python
categories:
 - Python
---

[TOC]

# Anacoda 环境变量
Anacoda 安装时会有加入环境变量的选项, 没有勾选的话手动加一下. 与 `JDK` 环境变量差不多:[环境变量配置](https://blog.csdn.net/u013211009/article/details/78437098)

# Anacoda 配置优化
`Anacoda` 安装完毕后修改 `pip` 源, 默认是国外服务器, 下载安装较慢.

命令行修改源和配置文件修改源取一个即可.

## 命令行修改`pip`源

上海交大`pip`源:
```bash
conda config --add channels https://mirrors.sjtug.sjtu.edu.cn/anaconda/pkgs/free/ 
conda config --add channels https://mirrors.sjtug.sjtu.edu.cn/anaconda/cloud/conda-forge/
conda config --set show_channel_urls yes
```

## 配置修改`pip`源
### Windows
在 `c:\user\xxxName\pip\pip.ini` 中加入
```
[global]
# 清华源
index-url=https://pypi.tuna.tsinghua.edu.cn/simple 
[install]  
trusted-host=pypi.tuna.tsinghua.edu.cn
disable-pip-version-check = true  
timeout = 6000  
```
需要 `创建pip文件夹 与 pip.ini 文件`。

### Linux 
```
cd $HOME  
mkdir .pip  
cd .pip
sudo vim pip.conf  

在里面添加  
[global]  
index-url=https://pypi.tuna.tsinghua.edu.cn/simple
[install]  
trusted-host=pypi.tuna.tsinghua.edu.cn 
disable-pip-version-check = true  
timeout = 6000 
```

# Anacoda 常用命令
- activate xxx 激活环境
- conda info --envs 查看所有环境
- conda create -n tensorflow pip 创建一个 tensorflow 环境
- conda remove -n tensorflow --all  删除环境
- conda env create -f  d:\python36_20190106.yml 从配置文件导入环境
- conda env export --file python36_20190106.yml 导出环境 到yml文件
- pip install matplotlib 安装模块
- conda create -n py27 python=2.7 创建一个py2.7
- conda clean --packages --tarballs | conda clean -a 清除缓存, 安装出错时可使用


# JupyterNotebook
`Anacoda` 默认会安装 `JupyterNotebook`, 这是一个类`wbe`版的 `python` 执行工具. `python` 执行实时可见.

## JupyterNotebook 配置修改
修改 JupyterNotebook 根目录: [windows下设置JupyterNotebook默认目录](https://www.cnblogs.com/tielemao/p/9787966.html)

设置完毕后可将 `JupyterNotebook笔记.7z` 压缩包解压到该目录, 配置好相关依赖即可直接执行里面的代码了.

## 将Anacoda创建的环境导入到JupyterNotebook中
`Anacoda` 创建的环境是不会自动导入到 `JupyterNotebook` 中的, 需要手动执行:
1. 创建`python 3.6`环境: `conda create -n py36 python=3.6`
2. 激活至 `py36` 环境: `activate py36`
3. 添加至 `JupyterNotebook` 中:
```Python
pip install ipykernel  # 可能会报错, No module named 'setuptools._deprecation_warning`, 重新安装一下pip install -U setuptools 再执行.
python -m ipykernel install --name python3.6 # 安装kernel, 再次打开notebook, 新建即可看到python3.6
```
安装完毕后, 在该`Anacoda`环境下安装的依赖在`JupyterNotebook`的`python3.6`内核中都可使用.

# OpenCV 安装
> 3.4.2 以后因部分算法被申请专利, 在开源版本中已移除, 推荐使用 3.4.1.15版本, 所有算法均可使用.

## OpenCV 相关whl

whl文件已在压缩包 `JupyterNotebook笔记.7z\OpenCV\环境配置库\` 目录下

3.4.1.15 依赖在线下载地址:
- [opencv_contrib_python-3.4.1.15-cp36-cp36m-win_amd64](
https://pypi.tuna.tsinghua.edu.cn/packages/2f/70/a688a6fc8d367c9bc78e27b8dc419daa8320f78e890af90b666aa7a91609/opencv_contrib_python-3.4.1.15-cp36-cp36m-win_amd64.whl#sha256=37371c2537478452de2b860a2e4903804ac70ae2660194b3d360dd433f874636)
- [opencv_python-3.4.1.15-cp36-cp36m-win_amd64.whl](https://pypi.tuna.tsinghua.edu.cn/packages/2b/31/cc5cf31258dc2cbb50dd1b046164add33804eab7af036d86aa68d45c7f6c/opencv_python-3.4.1.15-cp36-cp36m-win_amd64.whl#sha256=2cea809afe32cdccf97d66b131afc0fed80076244fb77b68ad9bf016d9cdc261)

## OpenCV 安装

```bash
conda create -n py36 python=3.6 # 已安装的就不需要执行这个了
activate py36
```

### 安装OpenCV whl
```
pip install opencv_python-3.4.1.15-cp36-cp36m-win_amd64.whl
pip install opencv_contrib_python-3.4.1.15-cp36-cp36m-win_amd64
```

### 安装OpenCV使用过程中的相关依赖
文件在压缩包 `JupyterNotebook笔记.7z\OpenCV\环境配置库\` 目录下
```
pip install matplotlib-3.1.2-cp36-cp36m-win_amd64.whl
pip install numpy-1.17.4-cp36-cp36m-win_amd64.whl
```