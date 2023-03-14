---
title: "抓包使用"
date: 2023-03-14T00:00:00+08:00
lastmod: 2023-03-14T00:00:00+08:00
author: ["藏锋"]
keywords: 
- mybatis
categories: 
- tech
tags: 
- java
- mybatis
description: "消息抓包使用"
weight:
slug: ""
draft: true # 是否为草稿
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

# 背景

tcp编码过程中，需要对一些消息进行抓包处理，这时候就需要用到了，主要涉及Windows及Linux系统；

## Windows下

win下，可以直接使用图像界面工具，wireshark 进行抓包，或者对Linux下抓的包进行分析

## Linux

使用命令行工具，如tcpdump
```
tcpdump -i 网卡名 -X port 端口号 -w 文件名.pcap
tcpdump -i 网卡名 -X port 端口号 -w 文件名.pcap
tcpdump tcp port 80 指定端口，src 表示源。
tcpdump tcp portrange 1-1024  指定端口范围。 dst 表示目标。
tcpdump host 192.168.1.113  使用host指定需要监听的主机
greater 1000 只抓取大于1000字节的数据包。
less 10  只抓取小于10字节的数据包。
tcpdump tcp and src 192.168.1.112 and port 8080

tcpdump host 210.27.48.1 and \ (210.27.48.2 or 210.27.48.3 ) 
截获主机210.27.48.1 和主机210.27.48.2 或210.27.48.3的通信

tcpdump udp port 123  监视本机的udp 123 端口

使用tcpdump抓取HTTP包
tcpdump  -XvvennSs 0 -i eth0 tcp[20:2]=0x4745 or tcp[20:2]=0x4854
• 0x4745 为"GET"前两个字母"GE",
• 0x4854 为"HTTP"前两个字母"HT"。

使用-A参数能以ASCII码显示数据包。
tcpdump -c 1 -A
-X参数能16进制数与ASCII码共同显示数据包。

```
