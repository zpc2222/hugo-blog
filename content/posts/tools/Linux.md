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

---

## Shell命令
- 添加用户
>useradd -d /home/zpc -m zpc

- 查看端口是否开放
>/sbin/iptables -L -n

- 开端口
>firewall-cmd --zone=public --add-port=81/tcp --permanent

- 重启防火墙
>systemctl restart firewalld.service

- 查看端口
>firewall-cmd --list-ports

- 安装JKD
>wget [https://download.oracle.com/java/17/latest/jdk-17_linux-x64_bin.tar.gz](https://download.oracle.com/java/17/latest/jdk-17_linux-x64_bin.tar.gz)

- 安装jdk17
```shell
/usr/lib/jvm/java-17-openjdk-amd64/

tar -zxvf jdk-17_linux-x64_bin.tar.gz

sudo mv jdk-17.0.1 /usr/local/

sudo vim /etc/profile

export JAVA_HOME=/usr/local/jdk-17.0.1

export CLASSPATH=.:JAVA_HOME/lib

export PATH=.:JAVA_HOME/bin:$PATH

source /etc/profile
```

### 压缩命令
- tar.gz 使用tar命令进行解压
>tar -zxvf java.tar.gz

- gz文件的解压 gzip 命令
>gzip -d java.gz

- 解压gz文件到特定目录,tar.gz包内提取某个文件在指定目录下
tar包
>tar tvf yourtarfile |grep fileyouwant，
  tar xvf yourtarfile fileyouwant(copy上面的全路径用绝对路径)

- tar.gz包
>tar ztvf yourtargzfile |grep fileyouwant，
   tar zxvf yourtarfile fileyouwant(copy上面的全路径用绝对路径)
   
- tar负责打包，gzip负责压缩
>tar
-c: 建立压缩档案
-x：解压
-t：查看内容
-r：向压缩归档文件末尾追加文件
-u：更新原压缩包中的文件

这五个是独立的命令，压缩解压都要用到其中一个，可以和别的命令连用但只能用其中一个。下面的参数是根据需要在压缩或解压档案时可选的。

>-z：有gzip属性的
-j：有bz2属性的
-Z：有compress属性的
-v：显示所有过程
-O：将文件解开到标准输出
 