---
title: git-learn
published: 2026-10-24
pinned: false
description: git-learn
tags: [git, stack]
image: 'api'
category: stack
slug: git
draft: false
---

## git worktree

```bash
# 首先克隆裸仓库
git clone https://gitee.com/alexmaodali/cilicili.git --bare cilicili.git
# 进入git记录文件
cd cilicili.git
# 创建master工作树
git worktree add ../master master
# 继续创建feature工作树
git worktree -b feature/danmu ../feature-danmu
# 查看当前目录结构
cd ..; tree .;
# 目录如下
cilicili-workspace
   -     cilicili.git
   -     feature-danmu
   -     hotfix
   -     master
```

## git bundle

```bash
# 电脑A打包 将bundle文件拷贝到电脑B
git bundle create cilicili.bundle dev prod
# 增量打包：只打包 oldhash..main 新增提交
git bundle create inc.bundle oldhash..main
# 电脑B进行提取
# 1. 新建空裸仓
git init --bare cilicili.git
cd cilicili.git
# 2. 从bundle拉取所有分支进来（最稳，不会碰HEAD问题）
git fetch ..\cilicili.bundle dev:dev prod:prod
git worktree add ../dev dev
```
