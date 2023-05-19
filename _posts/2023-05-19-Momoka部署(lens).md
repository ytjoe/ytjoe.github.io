---
layout: post
title: Momoka部署（lens）
date: 2023-05-11
tags: web3
---

## 环境
* ubuntu docker

## 创建POLYGON网络NODE_URL
* 使用 alchemy.com 创建节点
* 选择 POLYGON POS 主网络，创建保存 https 链接

## 安装docker
```shell
# 安装curl
apt-get install curl

# 更新
apt update

# 安装docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
rm -f get-docker.sh
```

## 创建Dockerfile文件,并build docker镜像
```shell
# 创建文件夹
mkdir /opt/momoka -p

# 创建Dockerfile文件 
cat > /opt/momoka/Dockerfile << EOF
FROM node:18-alpine AS base

# Install pnpm through corepack
RUN apk update
RUN apk add --no-cache libc6-compat
RUN corepack enable
RUN corepack prepare pnpm@latest --activate

# Specify a path for pnpm global root
ENV PNPM_HOME=/usr/local/bin

FROM base as runtime

# Install stable version of momoka
RUN pnpm add -g @lens-protocol/momoka

# Run using the default shell, this is important to infer the environment variables
CMD ["sh", "-c", "momoka --node $NODE_URL --environment=$ENVIRONMENT --concurrency=$CONCURRENCY --fromHead=true"]
EOF

# 进入文件夹
cd /opt/momoka/

# build docker镜像
docker build -t momoka:v1 .
```

## 运行momoka节点
```shell
docker run -it -d --name=momoka-node -e node=<my-POLYGON-URL> -e environment=POLYGON -e concurrency=20 momoka:v1
```

## 查看运行日志
```shell
docker logs momoka-node
```

## 参考文档
```shell
https://github.com/lens-protocol/momoka/tree/master/momoka-node
https://momoka.lens.xyz/
```