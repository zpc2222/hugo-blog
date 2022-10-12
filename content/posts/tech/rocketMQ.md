---
title: "rocketMQ"
date: 2022-09-05T00:17:58+08:00
lastmod: 2022-09-05T00:17:58+08:00
author: ["藏锋"]
keywords: 
- jvm
categories: 
- tech
tags: 
- java
- rocketMQ
description: "rocketMQ的命令使用"
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

 ```shell
### 1.启动nameserver
nohup sh bin/mqnamesrv &
#### 2.启动broker
nohup sh bin/mqbroker -n 192.168.1.100:9876 -c conf/broker.conf autoCreateTopicEnable=true &

## 查看日志
tail -f ~/logs/rocketmqlogs/namesrv.log
tail -f ~/logs/rocketmqlogs/broker.log
查看所有topic
sh mqadmin topicList -n 192.168.1.100:9876

## 1.关闭NameServer
sh bin/mqshutdown namesrv
## 2.关闭Broker
sh bin/mqshutdown broker
```