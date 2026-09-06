---
title: ElasticSearch学习
published: 2026-09-06
pinned: false
description: ElasticSearch学习
tags: [es, stack]
image: 'api'
category: es
slug: es-learning
---

## 参考

https://www.bilibili.com/video/BV1Gh411j7d6

## 文档

es的记录会按照文档存储。一条数据，就是一个document。文档数据会序列化为json进行存储。

## 索引

索引就是相同文档的集合，类似MySQL中的表

![image-20260905193946180](http://imgbed.alexmaodali.dpdns.org/file/default-imgbed/1788608402557_image-20260905193946180.png)

## 安装

安装es需要安装es还有kibana

[Kibana - Elastic](http://localhost:5601/app/home#/)

## 体验分词器

```http
POST /_analyze
{
  "text": ["java真是太棒了"],
  "analyzer": "standard"
}
```

```json
{
  "tokens" : [
    {
      "token" : "java",
      "start_offset" : 0,
      "end_offset" : 4,
      "type" : "<ALPHANUM>",
      "position" : 0
    },
    {
      "token" : "真",
      "start_offset" : 4,
      "end_offset" : 5,
      "type" : "<IDEOGRAPHIC>",
      "position" : 1
    },
    {
      "token" : "是",
      "start_offset" : 5,
      "end_offset" : 6,
      "type" : "<IDEOGRAPHIC>",
      "position" : 2
    },
    {
      "token" : "太",
      "start_offset" : 6,
      "end_offset" : 7,
      "type" : "<IDEOGRAPHIC>",
      "position" : 3
    },
    {
      "token" : "棒",
      "start_offset" : 7,
      "end_offset" : 8,
      "type" : "<IDEOGRAPHIC>",
      "position" : 4
    },
    {
      "token" : "了",
      "start_offset" : 8,
      "end_offset" : 9,
      "type" : "<IDEOGRAPHIC>",
      "position" : 5
    }
  ]
}
```

## 常见ES操作

查看飞书文档
