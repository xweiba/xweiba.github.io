---
title: cmd 命令
date: 2022-12-23 14:10:02
tags:
 - cmd
 - 常用命令
categories:
 - cmd
---

- 查看文件效验码
```
certutil -hashfile filename MD5
certutil -hashfile filename SHA1
certutil -hashfile filename SHA256
```

- 查看占用端口进程
```
netstat -ano | findstr "9559"
```

- 杀进程
```
taskkill /f /pid 28492
```

- 批量杀进程
```
taskkill /F /im frontpg.exe
```

## 生成目录接口样式

1.tree 默认显示文件夹

![image-20221223143630710](Cmd%20%E5%91%BD%E4%BB%A4/image-20221223143630710.png)

2.tree /f 显示文件

![image-20221223143643243](Cmd%20%E5%91%BD%E4%BB%A4/image-20221223143643243.png)

3.tree /a 显示目录状态 有文件的为+

![image-20221223143651963](Cmd%20%E5%91%BD%E4%BB%A4/image-20221223143651963.png)