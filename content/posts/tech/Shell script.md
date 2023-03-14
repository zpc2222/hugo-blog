---
title: "Shell脚本"
date: 2022-09-05T00:17:58+08:00
lastmod: 2023-03-14T00:17:58+08:00
author: ["藏锋"]
keywords: 
- jvm
categories: 
- tech
tags: 
- 脚本
- shell
description: "常用的一些shell语法"
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

## jar启动脚本
```shell
java  -Xms1024m -Xmx1024m -Xmn512m -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:7665 -jar oc-app-test.jar &
java -jar-Dnet.java.games.input.librarypath=/home/oc/oc-app-test -Xdebug-Xrunjdwp:transport=dt_socket,suspend=n,server=y,address=*:7665/home/oc/oc-app-test/*.jar &

```

## jar启动脚本2
```shell
#!/bin/sh

#根据端口号查询对应的pid
#pid=$(netstat -nlp | grep :$port | awk '{print $7}' | awk -F"/" '{ print $1 }');
pid=$(ps -ef | grep '[j]ava -jar -Dnet.java.games.input.librarypath=/home/iom/nc-cloud' | awk '{print $2}');

#杀掉对应的进程，如果pid不存在，则不执行
if [  -n  "$pid"  ];  then
    kill $pid;
    sleep 2
fi

java -jar -Dnet.java.games.input.librarypath=/home/iom/nc-cloud -Xdebug -Xrunjdwp:transport=dt_socket,suspend=n,server=y,address=*:7666 /home/iom/nc-cloud/*.jar --spring.profiles.active=dev &
```

## Jenkins使用的shell脚本：

``` shell
#/bin/bash
#前提：压缩包格式为appname-bin.tar.gz 解压出来的文件夹为appname 例如 app-dev-bin.tar.gz  app-dev
#下面appname配置要操作的应用名，例app-dev
appname=app-dev
echo starting kill $appname......
#grep -w 完全匹配关键字，打印对应进程名
pid=`ps -ef|grep -w $appname|grep java|awk '{printf $2}'`
echo "$appname pid is : $pid"
#判断进程是否存在，存在则kill、删除原程序目录、解压压缩包、启动程序
if [ -z $pid ];
then
echo "$appname is not running!"
echo "start rm /home/$appname"
rm -rf /home/$appname
echo "tar -zxf $appname-bin.tar.gz"
#解压到指定目录
tar -zxf /home/$appname-bin.tar.gz -C /home
echo "starting $appname...."
#nohup启动应用，日志输出到对应目录
nohup java -jar /home/$appname/$appname.jar >/home/log/$appname.log 2>&1 &
echo "start $appname  success!"
else
echo "start kill $pid "
kill -9 $pid
echo "end kill $pid "
echo "start check $appname ...... "
#睡眠3秒防止进程kill延迟
sleep 3
#检查是否杀死进程，杀死则重新部署应用，失败退出
check=`ps -ef|grep -w $pid|grep java|awk '{printf $2}'`
if [ -z $check ];
then
echo "$appname:$pid is killed success!"
echo "start rm /home/$appname"
rm -rf /home/$appname
echo "tar -zxf $appname-bin.tar.gz"
tar -zxf /home/$appname-bin.tar.gz -C /home
echo "starting $appname...."
nohup java -jar /home/$appname/$appname.jar >/home/log/$appname.log 2>&1 &
echo "start $appname  success!"
else
echo "$appname  stop failed !!!!"
fi
fi

```

## systemctl 方式 
```

守护进程重启 sudo systemctl daemon-reload
重启docker服务 sudo systemctl restart docker
关闭 dockersudo systemctl stop docker
service 方式
重启docker服务 sudo service docker restart
关闭 dockersudo service docker stop
添加用户	useradd -d /home/zpc -m zpc
查看端口是否开放	/sbin/iptables -L -n
开端口	firewall-cmd --zone=public --add-port=81/tcp --permanent
重启防火墙	systemctl restart firewalld.service
查看端口	firewall-cmd --list-ports

```

## 安装jdk17

```
wget https://download.oracle.com/java/17/latest/jdk-17_linux-x64_bin.tar.gz
安装jdk17

/usr/lib/jvm/java-17-openjdk-amd64/
tar -zxvf jdk-17_linux-x64_bin.tar.gz
sudo mv jdk-17.0.1 /usr/local/
sudo vim /etc/profile
export JAVA_HOME=/usr/local/jdk-17.0.1
export CLASSPATH=.:JAVA_HOME/lib
export PATH=.:JAVA_HOME/bin:$PATH
source /etc/profile
```

