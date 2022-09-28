---
title: "Linux"
date: 2022-09-28T00:14:01+08:00
lastmod: 2022-09-28T00:14:01+08:00
author: ["藏锋"]
keywords: 
- git
- tools
categories: 
- tools
tags: 
- tools
description: "Linux语句，语法及问题"
weight:
slug: ""
draft: false # 是否为草稿
comments: true
reward: true # 打赏
mermaid: true #是否开启mermaid
showToc: true # 显示目录
TocOpen: true # 自动展开目录
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示路径
cover:
    image: "" #图片路径例如：posts/tech/123/123.png
    caption: "" #图片底部描述
    alt: ""
    relative: false
---

 Shell：
添加用户
useradd -d /home/zpc -m zpc

查看端口是否开放
/sbin/iptables -L -n

开端口
firewall-cmd --zone=public --add-port=81/tcp --permanent

重启防火墙
systemctl restart firewalld.service

查看端口
firewall-cmd --list-ports

安装JKD
wget [https://download.oracle.com/java/17/latest/jdk-17_linux-x64_bin.tar.gz](https://download.oracle.com/java/17/latest/jdk-17_linux-x64_bin.tar.gz)

安装jdk17

/usr/lib/jvm/java-17-openjdk-amd64/

tar -zxvf jdk-17_linux-x64_bin.tar.gz

sudo mv jdk-17.0.1 /usr/local/

sudo vim /etc/profile

export JAVA_HOME=/usr/local/jdk-17.0.1

export CLASSPATH=.:JAVA_HOME/lib

export PATH=.:JAVA_HOME/bin:$PATH

source /etc/profile

