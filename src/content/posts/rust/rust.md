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

