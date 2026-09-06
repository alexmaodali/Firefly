---
title: Music项目设计与实现
published: 2026-09-06
pinned: false
description: Music项目设计与实现
tags: [stack]
image: 'api'
category: project
slug: music-project
---

> 本篇为Music项目的设计

## 项目定位

本项目为一个使用flutter制作的多端的音乐播放器项目，支持常见音乐平台的功能。

### 技术栈

前端：flutter

后端：SpringBoot MybatisPlus MySQL Redis ElasticSearch

### 核心功能

主要包括以下几个功能：

- 登录注册
- 我的关注
- 私信
- 音乐的推荐
	- 热门推荐
	- 个性化推荐
	- 音乐榜单

- 音乐播放界面
	- 歌词滚动
	- 评论互动
	- 音乐相关推荐
- ES搜索

- 歌单
	- 歌单分类
	- 歌单详情
	- 歌单评论
	- 歌单相关推荐
- 广场
	- 发布个人随笔的地方，post支持图片
- VIP体系：付费歌曲，只能是vip才能听。
- 音乐创作：AI创作+个人音乐上传
- 商城
	- 商品详情
	- 扫码支付

## 数据库设计

项目将采用MySQL数据库。

### 用户信息

- id: DBID
- email: 登录邮箱
- password：登录密码
- nickname：昵称
- brief：个性签名
- area：地域
- gender：性别
- birthday：生日
- avatar：头像
- status：0启用 1关闭

### 歌曲信息

- id：dbid

- title：歌曲标题

- artist：艺人

- duration：时长

- status：0启用 1关闭

	

