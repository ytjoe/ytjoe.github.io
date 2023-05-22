---
layout: post
title: docker安装chrome浏览器
date: 2023-05-22
tags: 运维
---

## 环境
* ubuntu docker
* 部署完成后虚拟环境
    + ubuntu22.04系统
    + chrome浏览器
    + VNC访问和web访问（无访问密码或有访问密码）

## 安装docker
```shell
# 安装docker
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
rm -f get-docker.sh
```

## 下载github仓库，修改并且build 
```shell
# 下载github仓库，并且必须带有--recursive，否则子项目会缺失
# 未添加recursive会导致该问题 https://github.com/fcwu/docker-ubuntu-vnc-desktop/issues/278
git clone --recursive https://github.com/fcwu/docker-ubuntu-vnc-desktop


# 修改Dockerfile文件，未注释，会导致该问题 https://github.com/fcwu/docker-ubuntu-vnc-desktop/issues/188
# 注释第一个出现的 RUN sed -i 's#http://archive.ubuntu.com/ubuntu/#mirror://mirrors.ubuntu.com/mirrors.txt#' /etc/apt/sources.list;
cd docker-ubuntu-vnc-desktop/
sed -i '0,/RUN/s//#RUN/' Dockerfile.amd64


# 修改 Dockerfile.amd64 在 EXPOSE 80 上一行插入chrome浏览器安装程序
################################################################################
# chrome
################################################################################
RUN apt-get update && apt-get install -y wget\
    && wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb\
    && apt-get install -y -f ./google-chrome-stable_current_amd64.deb\
    && sed -e '/chrome/ s/^#*/#/' -i /opt/google/chrome/google-chrome\
    && echo 'exec -a "$0" "$HERE/chrome" "$@" --user-data-dir="$HOME/.config/chrome" --no-sandbox --disable-dev-shm-usage' >> /opt/google/chrome/google-chrome


# build镜像 
docker build -t ubuntu-chrome:v1 .
```

## 启动
```shell
# 参数说明
# -e RESOLUTION=1920x800 设置web页面大小
# -e HTTP_PASSWORD=mypassword 为密码，可以不添加该参数则无密码
# -e USER=doro 默认使用root登录，也可以设置其他用户
# 端口80为web页面，5090为VNC端口
# shm导致浏览器崩溃https://github.com/fcwu/docker-ubuntu-vnc-desktop/issues/112
docker run -d --name chrome_1 -p 6080:80 -p 5900:5900 --shm-size 2g -e RESOLUTION=1920x800 -e HTTP_PASSWORD=mypassword ubuntu-chrome:v1

# 启动后可以通过 6080 端口访问web页面
```

## 参考内容
* ubuntu-desktop-lxde-vnc docker hub: `https://hub.docker.com/r/dorowu/ubuntu-desktop-lxde-vnc`
* ubuntu-desktop-lxde-vnc github: `https://github.com/fcwu/docker-ubuntu-vnc-desktop`
* ubuntu chrome浏览器安装: `https://blog.csdn.net/qq_21415979/article/details/121851384`
