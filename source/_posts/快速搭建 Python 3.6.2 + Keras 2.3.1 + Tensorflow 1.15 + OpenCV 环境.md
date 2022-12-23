---
title: 快速搭建 Python 3.6.2 + Keras 2.3.1 + Tensorflow 1.15 + OpenCV 环境
date: 2020-08-20 14:04:29
tags:
 - Python 
 - Keras
 - Tensorflow
 - OpenCV
 - 环境
categories:
 - Python
---

# 快速搭建环境
> 使用 `Anacoda`, 初始化环境  
> Keras 与 Tensorflow 版本依赖关系: [List of Available Environments - FloydHub Documentation](https://docs.floydhub.com/guides/environments/)

1. 创建环境并激活: 
```python
cnoda create -n keras_2.3.1 python=3.6
conda activate keras_2.3.1
```
2. 安装`2.3.1`版本`keras`:
```
pip install keras==2.3.1
```
3. 安装`1.15`版本`tensorflow`:
```
pip install --upgrade setuptools #先升级一下setuptools 不然装tensorflow会报缺少依赖错误
pip install tensorflow==1.15
```
4. 因为版权问题, `OpenCV` 后期版本移除了一些算法, 安装算法比较完整的`OpenCV`版本: 
```
pip install opencv-contrib-python==3.4.1.15
pip install opencv-python==3.4.1.15
```
5. 常用依赖: `Pillow` `matplotlib` `numpy`