---
title: "Windows"
date: 2022-09-05T00:17:58+08:00
lastmod: 2022-09-05T00:17:58+08:00
author: ["藏锋"]
keywords: 
- jvm
categories: 
- tech
tags: 
- 脚本
- bat
description: "windows命令"
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

# windows命令
1. 查看端口占用PID
   netstat -aon|findstr "9000"

   查看进程  tasklist|findstr "9088"
   杀进程      taskkill /T /F /PID 9088
