---
title: Hexo + Typora Markdown 图片路径设置
date: 2020-09-25 13:46:43
tags: 
 - Hexo
 - Typora
categories: 
 - Hexo
 - Typora
---

> 看了一圈图床,  貌似七牛最方便, 准备用的时候发现要绑定域名, 而且必须是备案过的, 暂时手头没有, 先存到本地吧...

# 修改 Hexo 和 Typora 的配置

首先修改 `hexo` 全局配置文件 `_config.yml` 中的配置：

```yml
post_asset_folder: true
```

这样在我们每次新建Markdown文件的时候，都会创建一个与文件同名的文件夹用于存放图片。

```linux
xweiba.github.io/source/_posts (source)
$ ll -a
total 13
drwxr-xr-x 1 xiaow 197121    0 9月  25 13:53 ./
drwxr-xr-x 1 xiaow 197121    0 9月  25 12:59 ../
drwxr-xr-x 1 xiaow 197121    0 9月  25 13:46 Hexo-Typora-Markdown-图片路径设置/
-rw-r--r-- 1 xiaow 197121  502 9月  25 13:53 Hexo-Typora-Markdown-图片路径设置.md
drwxr-xr-x 1 xiaow 197121    0 9月  25 13:03 Travis-CI-Hexo-实现自动构建部署GitHub-Pages/
-rw-r--r-- 1 xiaow 197121 6757 9月  25 13:04 Travis-CI-Hexo-实现自动构建部署GitHub-Pages.md
```

修改  `Typora` 图像配置: 文件-偏好设置-图像

![image-20200925142032094](Hexo-Typora-Markdown-%E5%9B%BE%E7%89%87%E8%B7%AF%E5%BE%84%E8%AE%BE%E7%BD%AE/image-20200925142032094.png)

# 安装图片插件

在项目目录下安装 `hexo-image-link` 图片插件(其他插件都有问题):

```npm
nom install hexo-image-link --save
```

# 启动Hexo看看有木有问题吧~~

```hexo
hexo s
```

首页:![image-20200925142825997](Hexo-Typora-Markdown-%E5%9B%BE%E7%89%87%E8%B7%AF%E5%BE%84%E8%AE%BE%E7%BD%AE/image-20200925142825997.png)

详情页:![image-20200925142744251](Hexo-Typora-Markdown-%E5%9B%BE%E7%89%87%E8%B7%AF%E5%BE%84%E8%AE%BE%E7%BD%AE/image-20200925142744251.png)

都是木有问题的啦, 提交 `git` 自动部署啦~~ 

网上的教程可能是老版本的, 配置后图片一直会报`404`, =.= 