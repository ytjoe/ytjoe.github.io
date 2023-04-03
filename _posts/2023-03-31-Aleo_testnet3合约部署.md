---
layout: post
title: Aleo_testnet3合约部署
date: 2023-03-31
tags: web3
---

## 环境
* Ubuntu 22.04
* leo
* snarkos

## 项目地址
* 官网: https://www.aleo.org/
* 官方教程: https://developer.aleo.org/testnet/getting_started/deploy_execute_demo/
* snarkOS安装教程: https://github.com/AleoHQ/snarkOS

## 安装leo
```shell
# 下载leo https://github.com/AleoHQ/leo/releases
wget https://github.com/AleoHQ/leo/releases/download/v1.6.3/leo-v1.6.3-x86_64-unknown-linux-gnu.zip
# 安装unzip
apt install unzip -y
# 解压
unzip leo-v1.6.3-x86_64-unknown-linux-gnu.zip
mv leo /usr/local/bin/

# 检查是否安装
leo --help
```

## 安装snarkos
```shell
# 安装git
apt install git -y
# 克隆仓库
git clone https://github.com/AleoHQ/snarkOS.git
# 进入snarkOS文件夹
cd snarkOS
# 安装依赖
./build_ubuntu.sh
# 安装snarkOS
cargo install --path .
``` 

## 创建钱包
打开网站 https://aleo.tools/ ，点击 Generate 生成Private Key、View Key、Address ，记录好这些值

## 领水 （需要很久，十几个小时）
```shell
发送推特，将地址替换为自己的地址，格式如下
@AleoFaucet send 10 credits to $YOUR_WALLET_ADDRESS

# 查询是否收到引用，查看推特引用中的链接 （目前大约需要16个小时才能领水成功）
获取链接中的值 object.execution.transitions[0].outputs[0].value
```

## 获取明文记录
```shell
导航到 https://aleo.tools/, 点击顶部 Record
将上一步值放入 Record (Ciphertext) 中，将创建私钥时View Key放入 View Key

注意： 不要点击Demo，最后获取 Record (Plaintext)
```

## 创建Leo应用程序
```shell
# 创建一个目录来存储您的 Leo 应用程序——您可以随意为该目录或位置使用不同的名称
cd $HOME/Desktop
mkdir demo_deploy_Leo_app && cd demo_deploy_Leo_app
# 将 $WALLETADDRESS 分配给你保存的钱包地址,输入前面记录下来的 Address 
WALLETADDRESS="aleo1nmsztf......"
# 使用部分钱包地址生成唯一的应用程序名称
APPNAME=helloworld_"${WALLETADDRESS:4:6}"
# 创建一个新的测试 Leo 应用程序
leo new "${APPNAME}"
# 运行您的 Leo 应用程序以确保一切正常(此处需要下载一些leo文件，可以提前下载好存放在 $HOME/.aleo/resources 中)
cd "${APPNAME}" && leo run && cd -
# 保存您的应用程序的路径 - 这在以后很重要
PATHTOAPP=$(realpath -q $APPNAME)
```

## 部署测试程序
```shell
# 进入程序部署目录
cd $PATHTOAPP && cd ..
# 将私钥填入以下参数中
PRIVATEKEY="APrivateKey1zk......"
# 将 获取明文记录Record (Plaintext) 填入以下参数
RECORD="{
  owner: aleo1nmsztf......,
  gates: 100000......,
  _nonce: 288974......
}"
# 部署 Leo 应用程序
snarkos developer deploy "${APPNAME}.aleo" --private-key "${PRIVATEKEY}" --query "https://vm.aleo.org/api" --path "./${APPNAME}/build/" --broadcast "https://vm.aleo.org/api/testnet3/transaction/broadcast" --fee 600000 --record "${RECORD}"
# 执行成功结果如下
✅ Successfully deployed 'helloworld_.......aleo' to https://vm.aleo.org/api/testnet3/transaction/broadcast.
at16x8......
```
## 执行应用程序
```shell
snarkos developer execute "${APPNAME}.aleo" "main" "1u32" "2u32" --private-key "${PRIVATEKEY}" --query "https://vm.aleo.org/api" --broadcast "https://vm.aleo.org/api/testnet3/transaction/broadcast"
# 执行成功结果如下
✅ Successfully broadcast execution 'helloworld_.......aleo/main' to the https://vm.aleo.org/api/testnet3/transaction/broadcast.
at139......
```