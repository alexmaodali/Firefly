---
title: Android环境搭建与Flutter项目运行
published: 2026-08-30
pinned: false
description: Android环境搭建与Flutter项目运行
tags: [android, flutter]
image: 'api'
category: android
slug: android-env
---

## JDK

- jdk需要安装17版本

## Android Studio

https://developer.android.google.cn/studio?hl=zh-cn

配置代理：

```
阿里云镜像：https://developer.aliyun.com/mirror/
豆瓣镜像：https://mirrors.douban.com/android/sdk/
华为镜像：https://developer.huawei.com/repo/
```

接受安卓协议

```shell
flutter doctor --android-licenses
```

安装Pixel 9 Pro模拟器

```python
# 检查环境
flutter doctor -v
```

### 运行项目到模拟器

补全各平台代码

```
flutter create .
```

配置镜像

```python
# gradle国内镜像地址：gradle-wrapper.properties
distributionUrl=https\://mirrors.cloud.tencent.com/gradle/gradle-8.14-all.zip
 
# gradle的maven仓库：settings.gradle.kts
    repositories {
        maven { url = uri("https://maven.aliyun.com/repository/google") }
        maven { url = uri("https://maven.aliyun.com/repository/releases") }
        maven { url = uri("https://maven.aliyun.com/repository/central") }
        maven { url = uri("https://maven.aliyun.com/repository/public") }
        maven { url = uri("https://maven.aliyun.com/repository/gradle-plugin") }
        maven { url = uri("https://maven.aliyun.com/repository/apache-snapshots") }
        maven { url = uri("https://maven.aliyun.com/nexus/content/groups/public/") }
        maven { url = uri("https://jitpack.io") }
        google()
        mavenCentral()
        gradlePluginPortal()
    }
 
 
# 安卓网络权限：AndroidManifest.xml
<uses-permission android:name="android.permission.INTERNET" />
```

### 运行模拟器

```shell
flutter emulators
Id          • Name        • Manufacturer • Platform
Pixel_9_Pro • Pixel 9 Pro • Google       • android
```

找到模拟器id，然后`flutter emulators --launch Pixel_9_Pro`先启动模拟器

```
flutter devices
Flutter assets will be downloaded from https://storage.flutter-io.cn. Make sure you trust this source!
Found 4 connected devices:
  sdk gphone16k x86 64 (mobile) • emulator-5554 • android-x64    • Android 17 (API 37) (emulator)
  Windows (desktop)             • windows       • windows-x64    • Microsoft Windows
  Chrome (web)                  • chrome        • web-javascript • Google Chrome 120.0.6099.201
  Edge (web)                    • edge          • web-javascript • Microsoft Edge 134.0.3124.93
```

找到模拟器id，然后`flutter run -d emulator-5554`使用模拟器启动项目

### 打包

```
flutter build apk --release
```

## 常见问题

### JDK版本

```
flutter config --jdk-dir="D:\MainDownload\OtherDownload\JDK17"
```

### NDK下载

```python
# 手动从国内镜像装着 NDK r27 → sdk\ndk\27.0.12077973\
https://mirrors.cloud.tencent.com/AndroidSDK/android-ndk-r27-windows.zip
```

