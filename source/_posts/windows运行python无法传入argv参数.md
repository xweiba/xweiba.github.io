---
title: windows运行python无法传入argv参数
date: 2020-07-29 14:05:30
tags:
 - windows
 - python
 - 错误记录
categories:
 - python
---

> 来自: [windows运行python无法传入argv参数](https://bbs.csdn.net/topics/392283593)

解决方法:
```
用python temp.py hello试一下是不是正常了
如果这样就可以了的话那就是设置映射的时候没带参数
虽然我也不理解怎么会有这么奇怪的事情发生
在注册表里搜索python.exe
看看关联启动的命令是不是
"......\python.exe " "%1" %*
后面的%*如果没有了那就是参数被忽略了
```

默认是没有 `%*` 的.
![](https://ws1.sinaimg.cn/large/b08fb805gy1g3fz0wc9e2j20s70j0wfe.jpg)