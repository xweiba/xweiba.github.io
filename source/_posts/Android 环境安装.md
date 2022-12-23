---
title: Android 环境安装
date: 2022-12-23 11:41:02
tags:
 - Android
 - 环境
categories:
 - Android
---

# Android SDK 安装

## 新建SDK文件夹并加入环境变量

- SDK主目录：`C:\Users\wb\Documents\Android\Android-SDK`
- 命令行工具：`C:\Users\wb\Documents\Android\Android-SDK\cmdline-tools`
- SDK 工具文件目录：`C:\Users\wb\Documents\Android\Android-SDK\platform-tools`
- 模拟器工具文件目录：`C:\Users\wb\Documents\Android\Android-SDK\emulator`
- SDK 工具文件目录：`C:\Users\wb\Documents\Android\Android-SDK\tools`
- SDK 工具文件目录：`C:\Users\wb\Documents\Android\Android-SDK\tools\bin`


### 系统环境变量新增：
1.变量名：`ANDROID_SDK_ROOT`，值：C:\Users\wb\Documents\Android\Android-SDK
2.变量名：`REPO_OS_OVERRIDE`，值：windows

### 系统环境变量PATH添加
1.`%ANDROID_SDK_ROOT%\cmdline-tools\bin`
2.`%ANDROID_SDK_ROOT%\platform-tools`

## Android 命令行工具安装
打开 `https://developer.android.google.cn/studio?hl=zh-cn#downloads`, 找到 `Command-line tools only` 下载并解压到 `%ANDROID_SDK_ROOT%\cmdline-tools` 目录

## 通过`cmdline-tools`安装SDK及调试工具包
> sdkmanager命令行使用说明：https://developer.android.google.cn/studio/command-line/sdkmanager?hl=zh-cn
> 确定安卓api等级：https://developer.android.google.cn/guide/topics/manifest/uses-sdk-element?hl=zh-cn

### sdkmanager
- `sdkmanager --list`: 查看SDK列表, `Flutter 支持API 16 (Android 4.1) 及更高的版本`
- `sdkmanager "platforms" "platforms;android-23" build-tools;30.0.3`：安装SDK及调试包
- `sdkmanager "system-images;android-27;google_apis_playstore;x86"` 安装模拟器系统包
- `sdkmanager "extras;intel;Hardware_Accelerated_Execution_Manager"` 安装模拟器依赖，打开sdk目录的`extras\intel\Hardware_Accelerated_Execution_Manager\haxm-7.6.5-setup.exe`
- `emulator.exe -list-avds` 查看已创建模拟器
- `emulator  -help-virtual-device` 模拟器创建帮助
- `android create avd -n <name> -t 1` 创建模拟器，没有删除命令，删除直接删除`C:\Users\wb\.android\avd`下的文件。

## 对应版本
```SDK
Android 13	33	TIRAMISU	平台亮点
Android 12	32	S_V2	平台亮点
            31	S	平台亮点
Android 11	30	R	平台亮点
Android 10	29	Q	平台亮点
Android 9	28	P	平台亮点
Android 8.1	27	O_MR1	平台亮点
Android 8.0	26	O	平台亮点
Android 7.1.1
Android 7.1	25	N_MR1	平台亮点
Android 7.0	24	N	平台亮点
Android 6.0	23	M	平台亮点
Android 5.1	22	LOLLIPOP_MR1	平台亮点
Android 5.0	21	LOLLIPOP
Android 4.4W	20	KITKAT_WATCH	仅限 KitKat for Wearables
Android 4.4	19	KITKAT	平台亮点
Android 4.3	18	JELLY_BEAN_MR2	平台亮点
Android 4.2、4.2.2	17	JELLY_BEAN_MR1	平台亮点
Android 4.1、4.1.1	16	JELLY_BEAN	平台亮点
Android 4.0.3、4.0.4	15	ICE_CREAM_SANDWICH_MR1	平台亮点
Android 4.0、4.0.1、4.0.2	14	ICE_CREAM_SANDWICH
Android 3.2	13	HONEYCOMB_MR2	
Android 3.1.x	12	HONEYCOMB_MR1	平台亮点
Android 3.0.x	11	HONEYCOMB	平台亮点
Android 2.3.4
Android 2.3.3	10	GINGERBREAD_MR1	平台亮点
Android 2.3.2
Android 2.3.1
Android 2.3	9	GINGERBREAD
Android 2.2.x	8	FROYO	平台亮点
Android 2.1.x	7	ECLAIR_MR1	平台亮点
Android 2.0.1	6	ECLAIR_0_1
Android 2.0	5	ECLAIR
Android 1.6	4	DONUT	平台亮点
Android 1.5	3	CUPCAKE	平台亮点
Android 1.1	2	BASE_1_1	
Android 1.0	1	BASE	
```