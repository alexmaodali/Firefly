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

## git rebase

```bash
# 交互式变基操作 xxx -> 你要修改的提交的前一个提交
git rebase -i xxx 
# 中间提交操作
git fetch gitlab dev
git rebase gitlab/dev
```

## 忽略文件添加

```bash
git add . ":(exclude)*.yaml" ":(exclude)*.yml" ":(exclude)pom.xml"
```

## git zip

```bash
git archive -o mock-01.zip HEAD
```

## git sparse

```bash
ss-init
ss add yudao-module-stat yudao-denpendencies ... # 添加你需要的模块
```

## 当小偷

> 如果需要全量覆盖A目录，那么需要先删除A目录，再偷目标目录A

```shell
git checkout dev
git checkout dev-mzl-sss-v4 -- apps/web-antd/src/api/subsystem/
git checkout dev-mzl-sss-v4 -- apps/web-antd/src/views/subsystem/
# 假如你在dev-mzl-sss-v4开发好了 现在需要合并代码到dev
git co dev
git checkout dev-mzl-sss-v4 -- yudao-module-subsystem/
# 这样dev就有你dev-mzl-sss-v4对于yudao-module-subsystem/的修改了
```

## 当强盗

powershell可以直接偷 美哉

```shell
git checkout temp-redev-226 -- ":(glob)**/application*.yaml"
```

## Git仓库优化性能

```bash
# 预打包，减少大量松散对象扫描开销
git repack -d
# 开启 fsmonitor（Windows Git for Windows 自带，大幅加速 git status）
git config core.fsmonitor true
git config core.untrackedcache true
```

