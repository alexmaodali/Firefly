---
title: Oh-Your-Opencode
published: 2026-08-15
pinned: false
description: Oh-Your-Opencode
tags: [opencode]
image: "api"
category: stack
slug: oh-your-opencode
---


# oh-your-opencode

本文会教你如何让你的opencode更强大，记得开代理 👻

## rtk


rtk（Rust Token Killer） —— 一个 LLM token 优化工具，它会透明地将高冗长输出的 shell 命令（git、pnpm、vitest、test 等）

改写为压缩版本，从而减少喂给 AI agent 的 token 量

```bash
cargo install --git https://github.com/rtk-ai/rtk --locked
rtk --version
rtk gain        # 必须显示 savings dashboard，而非 "command not found"
rtk init --global --opencode
rtk init --show
# 功能验证
rtk git status
rtk ls .
```

## codegraph

[codegraph](https://github.com/colbymchenry/codegraph) 会把代码库预先索引成知识图谱，让 agent 一次查询就能拿到精确的上下文（调用链路、影响范围、相关符号），而不是逐个文件爬取。

**建议项目级别安装。** 先 `cd` 进入项目目录，然后把以下提示词粘贴到 OpenCode：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.opencode/refs/heads/main/docs/installation-codegraph.md
```

## caveman

[caveman](https://github.com/juliusbrussee/caveman) 是极致压缩的沟通模式，节省约 75% token 的同时保留技术准确性。

**建议项目级别安装。** 先 `cd` 进入项目目录，然后把以下提示词粘贴到 OpenCode：

```
Read the installation guide and follow it:
https://raw.githubusercontent.com/orionpax1997/kickstart.opencode/refs/heads/main/docs/installation-caveman.md
```

