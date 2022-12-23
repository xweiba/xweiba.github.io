---
title: OpenCV 环境准备
date: 2019-11-29 13:39:55
tags:
 - OpenCV
 - 环境
categories:
 - OpenCV
---

> 3.4.2 以后因部分算法被申请专利, 在开源版本中已移除, 推荐使用 3.4.1.15版本, 所有算法均可使用.

3.4.1.15 依赖下载地址:
- [opencv_contrib_python-3.4.1.15-cp36-cp36m-win_amd64](
https://pypi.tuna.tsinghua.edu.cn/packages/2f/70/a688a6fc8d367c9bc78e27b8dc419daa8320f78e890af90b666aa7a91609/opencv_contrib_python-3.4.1.15-cp36-cp36m-win_amd64.whl#sha256=37371c2537478452de2b860a2e4903804ac70ae2660194b3d360dd433f874636)
- [opencv_python-3.4.1.15-cp36-cp36m-win_amd64.whl](https://pypi.tuna.tsinghua.edu.cn/packages/2b/31/cc5cf31258dc2cbb50dd1b046164add33804eab7af036d86aa68d45c7f6c/opencv_python-3.4.1.15-cp36-cp36m-win_amd64.whl#sha256=2cea809afe32cdccf97d66b131afc0fed80076244fb77b68ad9bf016d9cdc261)

3.4.1.15 只有Python3.6版本的whl, Anaconda 默认 3.7, 将Python切换至3.6. 
```
conda create -n py36 python=3.6
activate py36
```

notebook 添加 3.6 环境, 在 py36 环境下运行:
```Python
pip install ipykernel  # 可能会报错, No module named 'setuptools._deprecation_warning`, 重新安装一下pip install -U setuptools 再执行.
python -m ipykernel install --name python3.6 # 安装kernel, 再次打开notebook, 新建即可看到python3.6
```

修改 JupyterNotebook 根目录请看: [windows下设置JupyterNotebook默认目录](https://www.cnblogs.com/tielemao/p/9787966.html)