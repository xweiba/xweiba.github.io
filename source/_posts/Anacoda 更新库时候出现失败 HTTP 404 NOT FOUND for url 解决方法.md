---
title: Anacoda 更新库时候出现失败 HTTP 404 NOT FOUND for url 解决方法
date: 2020-07-29 14:06:29
tags:
 - Python
 - Anacoda
categories:
 - Python
---

[toc]
> win10 & Anacoda

原来使用的清华源, 当不久前被关闭了...

# 一. 更换Anacoda源
打开 `Anacoda Prompt` 重新设置源即可. 这是上海交大的, 暂时还可以用, 好像没更新了.
```
conda config --add channels https://mirrors.sjtug.sjtu.edu.cn/anaconda/pkgs/free/ 
conda config --add channels https://mirrors.sjtug.sjtu.edu.cn/anaconda/cloud/conda-forge/
conda config --set show_channel_urls yes
```

再次下载即可~.