---
title: Dart和Flutter的学习
published: 2026-08-23
pinned: false
description: Dart和Flutter的学习
tags: [dart, flutter]
image: 'api'
category: flutter
slug: dart-learning
---

## 对应教程教程

https://www.bilibili.com/video/BV1wR4Xz6EqG

## Dart包管理器

pub.dev 是 Dart 和 Flutter 的官方包仓库，类似于 npm（JavaScript）或 PyPI（Python）。

## Dart-SDK安装

```shell
choco install dart-sdk
```

## Dart-语法教程

[菜鸟教程-Dart](https://www.runoob.com/dart/dart-async.html)

## Flutter安装

```shell
# 1.必须通过 git clone
git clone -b stable https://github.com/flutter/flutter.git
# 2.配置bin环境遍历
# 3.配置mirror 打开posh加入下面遍历
$env:PUB_HOSTED_URL="https://pub.flutter-io.cn";
$env:FLUTTER_STORAGE_BASE_URL="https://storage.flutter-io.cn";
# 4.flutter --version 自动下载sdk
# 5.诊断开发环境
flutter doctor
```

## 创建第一个Flutter项目

```shell
flutter create --platforms web demo_hello
cd demo_hello;
flutter run;
flutter run -d chrome
```

安装Flutter、Awesome Flutter Snippets插件

**Flutter一切皆Widget**

> Flutter主题默认Material

## 基础组件 MaterialApp

```dart
void main() {
  runApp(
    MaterialApp(
      title: "Flutter",
      theme: ThemeData(scaffoldBackgroundColor: Colors.blue),
      home: Scaffold(),
    ),
  );
}
```

## Scaffold

用于构建Material Design风格页面的核心布局组件 可以灵活配置页面骨架

```dart
void main() {
  runApp(
    MaterialApp(
      title: "Flutter",
      home: Scaffold(
        appBar: AppBar(
          title: Text("Top"),
        ),
        body: Container(
          child: Center(
            child: Text("Center"),
          ),
        ),
        bottomNavigationBar: Container(
          height: 80,
          child: Center(
            child: Text("Bottom"),
          ),
        ),
      ),
    ),
  );
}
```

## 自定义组件 Custom Widget

### 无状态组件 StatelessWidget

一旦创建，内部状态不可变 适合静态内容

- 单类
- 只有`build()`这个生命周期 构建或重新构建(父组件状态变化)时会执行

```dart
class CustomWidget extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: "Flutter",
      // theme: ThemeData(scaffoldBackgroundColor: Colors.blue),
      home: Scaffold(
        appBar: AppBar(title: Text("Top")),
        body: Container(child: Center(child: Text("Center"))),
        bottomNavigationBar: Container(
          height: 80,
          child: Center(child: Text("Bottom")),
        ),
      ),
    );
  }
}
void main() { runApp(CustomWidget()); }
```

### 有状态组件 StatefulWidget

可在生命周期内进行状态改变 适合交互式组件 比如计数器

- 两个类

- 生命周期


![image-20260823213225132](http://imgbed.alexmaodali.dpdns.org/file/default-imgbed/1787491962638_image-20260823213225132.png)


```dart
// 第一个类负责创建实例
class CustomStatefulWidget extends StatefulWidget {
  @override
  State<StatefulWidget> createState() {
    return _CustomStatefulWidgetState();
  }
}
// 第二个类负责处理视图
class _CustomStatefulWidgetState extends State<CustomStatefulWidget> {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: "Flutter",
      // theme: ThemeData(scaffoldBackgroundColor: Colors.blue),
      home: Scaffold(
        appBar: AppBar(title: Text("Top")),
        body: Container(child: Center(child: Text("Center"))),
        bottomNavigationBar: Container(
          height: 80,
          child: Center(child: Text("Bottom")),
        ),
      ),
    );
  }
}
```

## 点击事件 GestureDetector

```dart
body: Container(
  child: Center(
    child: GestureDetector(
      child: Text("Event"),
      onTap: () => print('Click'),
    ),
  ),
),
```

## 计数器实现

```dart
import 'package:flutter/material.dart';

class ClickComponnet extends StatefulWidget {
  const ClickComponnet({super.key});

  @override
  State<ClickComponnet> createState() => _ClickComponnetState();
}

class _ClickComponnetState extends State<ClickComponnet> {
  int count = 0;

  @override
  Widget build(BuildContext context) {
    return Container(
      child: MaterialApp(
        home: Scaffold(
          body: Row(
            children: [
              TextButton(
                onPressed: () {
                  setState(() {
                    count = count + 1;
                  });
                },
                child: Text("Plus"),
              ),
              Text(count.toString()),
            ],
          ),
        ),
      ),
    );
  }
}
```

## Container组件使用

![image-20260824071512739](http://imgbed.alexmaodali.dpdns.org/file/default-imgbed/1787526930826_image-20260824071512739.png)

```dart
class Test extends StatelessWidget {
  const Test({super.key});
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        body: Container(
          width: 200,
          height: 200,
          margin: EdgeInsets.all(20),
          transform: Matrix4.rotationZ(0.05),
          alignment: Alignment.center,
          decoration: BoxDecoration(
            color: Colors.blue,
            border: Border.all(width: 3.0, color: Colors.black),
            borderRadius: BorderRadius.circular(15),
          ),
          child: TextButton(
            onPressed: () {
              print("ClickContainer");
            },
            child: Text(
              "Hello Container",
              style: TextStyle(color: Colors.white, fontSize: 20),
            ),
          ),
        ),
      ),
    );
  }
}
```

## Center组件

```dart

```