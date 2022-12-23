---
title: Flutter 开发技巧.md
date: 2022-11-24 11:37:28
tags:
 - Flutter
 - 经验
categories:
 - Flutter
---

# 文档
- [Flutter Getx Doc](https://1467602180.github.io/flutter-getx-doc/)
- [API Doc](https://pub.flutter-io.cn/documentation/get/latest/index.html)
- 开源项目:[flutter_bolg_manage](https://github.com/jhflovehqy/flutter_bolg_manage)
- [《Flutter实战·第二版》](https://book.flutterchina.club/)

# 沉浸式状态栏
在main方法中添加：
```flutter
SystemChrome.setSystemUIOverlayStyle(
    const SystemUiOverlayStyle(
      statusBarColor: Colors.transparent,
      statusBarBrightness: Brightness.light,
    ),
);
```

# 内容居中
先使用Center使x轴居中，再使用Column的mainAxisAlignment: MainAxisAlignment.center属性实现y轴居中。
```flutter
Scaffold(
      appBar: AppBar(
        title: const Text("New Page"),
        centerTitle: true,
      ),
      body: Center(
          child: Column(
        mainAxisAlignment: MainAxisAlignment.center,
        children: [
          Obx((() => Text("Get find Controller count: ${controller.count}"))),
        ],
      )),
    )
```