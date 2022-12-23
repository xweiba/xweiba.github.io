---
title: CentOS7 忘记密码后修改
date: 2019-10-11 13:22:02
tags:
 - CentOS7
 - 忘记密码
 - 经验
categories:
 - CentOS7
---

> 网上的大部分教程都是坑。。

按这个教程来ok了： [CentOS7 忘记密码](https://www.jianshu.com/p/c6c226d3c3f0)

大致不走如下：

# 修改引导
修改这两处即可:
![image](CentOS7%20%E5%BF%98%E8%AE%B0%E5%AF%86%E7%A0%81%E5%90%8E%E4%BF%AE%E6%94%B9/SouthEast.png)
按 Ctrl+X 启动系统

# 进入shell
首先切换至 `root` 用户， 网上教程大多没有
```shell
chroot /sysroot
```

再使用 `passwd root` 修改密码。

最后 `touch /.autorelabel`, 在/目录下创建一个.autorelabel文件，有这个文件存在，系统在重启时就会对整个文件系统进行relabeling。

exit&&reboot