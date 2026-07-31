---
title: maven command
published: 2026-10-13
pinned: false
description: maven command
tags: [other]
image: 'api'
category: other
slug: maven command
draft: false
---

## 整体依赖下载

```bash
mvn clean install -U -DskipTests -T 4 -o
```

## 编译

```bash
mvn clean compile -pl yudao-module-stat/yudao-module-stat-server -am -DskipTests -T 4 -o
mvn clean compile -pl yudao-module-system/yudao-module-system-server -am -DskipTests -T 4 -o
```

## 打包

```bash
mvn clean package -pl yudao-module-system/yudao-module-system-server -am -DskipTests -T 4 -o
mvn clean package -pl yudao-module-stat/yudao-module-stat-server -am -DskipTests -T 4 -o
```

## api模块有变更

```bash
mvn clean install -pl yudao-module-system/yudao-module-system-api -am -DskipTests -T 4 -o
mvn clean compile -pl yudao-module-system/yudao-module-system-server -DskipTests -T 4 -o
```
