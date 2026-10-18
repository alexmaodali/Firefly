---
title: Rust从入门到入土
published: 2026-08-30
pinned: false
description: Rust从入门到入土
tags: [rust]
image: "api"
category: stack
slug: rust
---

## Rust简介

优点：

- 性能好
- 内存安全
- 并发好

参考学习视频：https://www.bilibili.com/video/BV1hp4y1k7SV

适合领域：

- 高性能Web Service
- WebAssembly
- 命令行工具
- 网络编程
- 嵌入式设备
- 系统编程

## Rust安装

进入官网直接下载

```python
# 更新
rustup update
# 卸载
rustup self uninstall
# 版本
rustc --version
# 本地文档
rustup doc
```

## Hello World

```rust
fn main() {
  println!("Hello World!");
}
```

运行

```python
rustc hello_world.rs
hello_world.exe
# pdb文件包含调试信息
```

> rustc只适合编译简单的程序，编译项目等推荐使用cargo

## Cargo

```python
# 创建项目
cargo new hello_cargo
# 运行 需要进入到项目目录
cargo run
# 编译
cargo build
# Check
cargo check
# Release 
cargo build --release
# 更新依赖版本
cargo update 
```

## 猜数游戏

```rust
use std::io; 
use rand::Rng;
use std::cmp::Ordering;

fn main() {
  let secret_number = rand::thread_rng().gen_range(1, 101);

  loop {
  let mut guess = String::new();
  io::stdin().read_line(&mut guess).expect("ERROR");
    // Shadow 隐藏同名变量
    let guess: u32 = match guess.trim().parse() {
        Ok(number) => number,
        Err(_) => continue,
    };

    println!("You input: {}", guess);

    match guess.cmp(&secret_number) {
        Ordering::Less => println!("Too Small"),
        Ordering::Greater => println!("Too Big"),
        Ordering::Equal => {
            println!("You win");
            break;
        },
    }   
  }
}
```

## 变量与常量

- 变量let声明默认为不可变变量，若要变成可变变量，需要使用`let mut`进行声明
- 变量可以声明在任意位置 `const MAX = 1000`

## Shadowing

- shadow后的变量类型可以与之前不一样，mut的变量不能修改变量类型

```rust
let x = 5;
let x = x + 1; // shadowing 后面使用的x为6
let x = x * 2; // 12
```

## 数据类型

### 标量类型

一个标量类型代表一个单个的值

- 整数类型：i8 u8 i16 i32 i64 i128 isize usize （isize和usize的使用场景主要是对某种集合进行索引操作）默认i32
- 浮点类型：f32单精度 f64双精度
- 布尔类型：bool == true false
- 字符类型：char 单个字符 'A' Unicode标量值

### 复合类型

- 元组 tuple

	> 一旦声明，长度不可改变
	>
	> ```rust
	> let tup: (i32, f64, u8) = (500, 6.4, 1) // tup.0 = 500
	> // 结构tuple
	> let (x, y, z) = tup;
	> ```

- 数组

	> ```rust
	> let arr: [i32, 2] = [1, 2]; // arr[0] = 1
	> let arr: [3; 3] // 相当于 [3, 3, 3] [初始值, 长度]
	> ```
	>
	> `Vector`是更加灵活的数组，推荐使用



