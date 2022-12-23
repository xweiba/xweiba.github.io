---
title: JupyterNotebook 配置
date: 2019-11-29 14:07:21
tags:
 - Python
 - JupyterNotebook
 - 配置
categories:
 - Python
---

## 修改 JupyterNotebook 根目录
- [windows下设置JupyterNotebook默认目录](https://www.cnblogs.com/tielemao/p/9787966.html)

## 安装扩展
conda install -c conda-forge jupyter_nbextensions_configurator

## 新增Python环境
notebook 添加 3.6 环境, 在 py36 环境下运行:
```Python
pip install ipykernel  # 可能会报错, No module named 'setuptools._deprecation_warning`, 重新安装一下pip install -U setuptools 再执行.
python -m ipykernel install --name python3.6 # 安装kernel, 再次打开notebook, 新建即可看到python3.6
```