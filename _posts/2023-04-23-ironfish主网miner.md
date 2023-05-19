---
layout: post
title: ironfish主网miner (bzminer+转发)
date: 2023-04-23
tags: web3
---

## 环境
* GPU服务器（ubuntu-server）
* 流量转发服务器（ubuntu-server）

## 矿池地址
* herominer: https://ironfish.herominers.com/#
* f2pool: https://www.f2pool.com/
* flexpool: https://www.flexpool.io/zh-CN/get-started

## 流量转发服务配置
> 用于不能连接到矿池的国内机器转发

```shell
# redir安装
apt-get update -y
apt-get install redir -y

# 配置转发，将本机1145进入的流量转发到sg.ironfish.herominers.com:1145
#（这里以herominer为例，其他矿池如f2pool同样配置转发，注意端口并且服务器防火墙/云服务器防火墙需要打开端口）
redir :1145 sg.ironfish.herominers.com:1145

# 检查转发（本机检查）
telnet -ntlp
# tpc 0 0 0.0.0.0:1145 0.0.0.0;* LISTEN 11655/redir 表示正常

# 检查转发（远程检查）
telnet 转发服务ip 1145
# Trying 转发服务ip...
# Connected to 转发服务ip.
# Escape character is '^]'. 表示正常
```

## GPU服务器配置
> 参考配置https://ironfish.herominers.com/#how-to-mine-ironfish-iron
> bzminer仓库 https://github.com/bzminer/bzminer/releases

```shell
1、显卡驱动安装（自行安装）

2、下载bzminer,解压，进入文件夹
wget https://github.com/bzminer/bzminer/releases/download/v14.2.2/bzminer_v14.2.2_linux.tar.gz && tar -zxvf bzminer_v14.2.2_linux.tar.gz && cd bzminer_v14.2.2_linux

3、创建ironfish挖矿配置
cat > ironfish.sh << EOF
#!/bin/sh
# nohup ./bzminer -a ironfish -w 矿工钱包地址.自定义名称 -p 代理地址 --nc 1 >> log &    下面是一个herominer的例子（使用f2pool配置需要按照f2pool配置账户名称）
nohup ./bzminer -a ironfish -w a7b38eb8515c2ba568285f44869a83116f53f53da15c5cd8786f11a2eecb6695.minern1 -p stratum+tcp://sg.ironfish.herominers.com:1145 --nc 1 >> log &
EOF

chmod +x ironfish.sh
4、执行挖矿
./ironfish.sh
```

## 查看挖矿状态

```shell
1、浏览器打开 https://ironfish.herominers.com/#
2、Your Stats & Payment History 中填写自己的钱包地址查看自己机器在线情况
```