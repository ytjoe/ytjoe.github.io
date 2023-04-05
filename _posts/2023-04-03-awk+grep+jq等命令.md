---
layout: post
title: awk+grep+jq等命令
date: 2023-04-03
tags: 运维
---

## awk

## grep

## jq
```shell
# jq格式化
curl -s http://.....| jq

# 过滤出js node.active
jq -r ".node.active"

# 修改jq过滤出的数据 (返回指定json格式)
curl -s http://127.0.0.1:8000/status|jq  "{ address: .address, isOnline: .network.nodeInfo.active }"
## 返回值如下
{
  "address": "0x.....",
  "isOnline": true
}

# 修改jq过滤出的数据 (返回指定文本格式)
curl -s http://61.155.145.72:8000/status|jq  -r '.address + "\n" + (.network.nodeInfo.active | tostring)'
## 返回值如下
0x......
true
```
