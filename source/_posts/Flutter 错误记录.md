---
title: Flutter 错误记录
date: 2022-12-23 11:39:12
tags:
 - Flutter
 - 经验
 - 异常记录
categories:
 - Flutter
---

# 错误记录
## `Android sdkmanager not found`
其实就是`cmdline-tools`没装好。使用`sdkmanager --install "cmdline-tools;latest"`安装一下就行了。

## Running Gradle task 'assembleDebug' 卡住
先停止运行，然后在项目中根目录运行 `flutter run -v` 即可看到卡在哪里了。
> https://blog.csdn.net/houor/article/details/115371120 Gradle 更换国内镜像

## You need Java 11 or higher to build your app with this version of Gradle.
使用指定JDK，在项目的`android/gradle.properties`添加`org.gradle.java.home=D:/resource/JAVA/JDK/jdk-11.0.2`属性。

模板位置：`D:\resource\Flutter\Flutter-SDK\packages\flutter_tools\templates\module\android\gradle\gradle.properties.tmpl`

## 'pub' 不是内部或外部命令，也不是可运行的程序
pub独立在2.10后已弃用，使用`dart pub` or `flutter pub` 代替。

## `get create project:my_project_1` 卡住
get-cli 1.7.1 版本bug, ctrl+z跳过

## `EXCEPTION CAUGHT BY SVG` 无法加载SVG图片
> 来源：[https://stackoverflow.com/questions/61202925/svgpicture-image-rendering-error-in-flutter](https://stackoverflow.com/questions/61202925/svgpicture-image-rendering-error-in-flutter)

svg文件格式有问题，可使用这个网站[https://jakearchibald.github.io/svgomg/](https://jakearchibald.github.io/svgomg/)导入后再导出。

## `A RenderFlex overflowed by 19 pixels on the bottom.` 键盘弹出时报错
元素被键盘顶起导致内容区溢出，使用`SingleChildScrollView`包裹即可，不需要滚动的设置属性：`physics: const NeverScrollableScrollPhysics(),`

## `fluttertoast` 无法在windows端使用
windows端适配比较麻烦，更换为overlay_support库

## `Connectivity().checkConnectivity()` 无法在windows端使用
connectivity更换为connectivity_plus库

## `canLaunchUrl(uriTemp)` 在Android端大于API 30时 一直返回false
在Android端大于API 30时对权限做了限制，必须在Android中添加以下配置：
```
<!-- Provide required visibility configuration for API level 30 and above -->
<manifest>
<queries>
  <!-- If your app checks for SMS support -->
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <data android:scheme="sms" />
  </intent>
  <!-- If your app checks for call support -->
  <intent>
    <action android:name="android.intent.action.VIEW" />
    <data android:scheme="tel" />
  </intent>
</queries>
</manifest>
```

## ` HandshakeException: Connection terminated during handshake` 