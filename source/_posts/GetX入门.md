---
title: GetX入门
date: 2022-12-23 11:40:19
tags:
 - Flutter
 - GetX
categories:
 - Flutter
---

# GetX

# 常用API

## GetBuild, GetX, Obx 使用场景
GetBuild: 性能为主使用这个，他的刷新由我们自己通过updata方法更新。
GetX: 项目中只有一个地方使用了Controller时使用，量比较少的时候，因为他的更新时基于流的，每次更新都会刷新所有引用的地方。
Obx: 如果项目中单个的东西比较多，使用Obx，但是Obx是每秒都在执行的，相当于有监听器。

## Get-Cli
Get 脚手架工具
### 安装
```cmd
flutter pub global activate get_cli
```
添加环境变量: `%FLUTTER_SDK_ROOT%\.pub-cache\bin`

### 常用命令
- get create project:my_getx_demo1
  - GetX Pattern:代码风格，MVC结构，客户端模式
  - CLEAN:代码风格，后端服务模式
- get create page:home
- Get.find<XXX>()

### Binding
Binding是用来引入依赖的，默认都是懒加载。可以在dependencies方法中引入其他控制器。