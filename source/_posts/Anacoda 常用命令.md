---
title: Anacoda 常用命令
date: 2020-09-09 14:03:40
tags:
 - Anacoda 
 - 常用命令
categories:
 - Python
---

- activate xxx 激活环境
- conda info --envs 查看所有环境
- conda create -n tensorflow pip 创建一个 tensorflow 环境
- conda remove -n tensorflow --all  删除环境
- conda env create -f  d:\python36_20190106.yml 从配置文件导入环境
- conda env export --file python36_20190106.yml 导出环境 到yml文件
- pip install matplotlib 安装模块
- conda create -n py27 python=2.7 创建一个py2.7
- conda clean --packages --tarballs | conda clean -a 清除缓存, 安装出错时可使用