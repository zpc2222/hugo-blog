---
title: "Linux及Shell使用"
date: 2022-09-05T00:17:58+08:00
lastmod: 2023-03-16T00:17:58+08:00
author: ["藏锋"]
keywords: 
- jvm
- linux
categories: 
- tech
tags: 
- Linux
- shell
description: "常用的一些shell语法(基于Linux系统)"
weight:
slug: ""
draft: false # 是否为草稿
comments: true
reward: true # 打赏
mermaid: true #是否开启mermaid
showToc: true # 显示目录
TocOpen: true # 自动展开目录
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: false # 底部不显示分享栏
showbreadcrumbs: true #顶部显示路径
cover:
    image: "" #图片路径例如：posts/tech/123/123.png
    caption: "" #图片底部描述
    alt: ""
    relative: false
---

## Linux命令
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


## java启动

### 纯Java启动
```shell
java  -Xms1024m -Xmx1024m -Xmn512m -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=*:7665 -jar test.jar &
java -jar-Dnet.java.games.input.librarypath=/home/test -Xdebug-Xrunjdwp:transport=dt_socket,suspend=n,server=y,address=*:7665/home/test/*.jar &

```

### 自动重启Java进程
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

## Jenkins自动部署

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

### 安装jdk17

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

## 运维使用脚本

###  根据 PID 显示进程所有信息**
```shell
#! /bin/bash

read -p "请输入要查询的PID: " P

n=`ps -aux| awk '$2~/^'${P}'$/{print $0}'|wc -l`

if [ $n -eq 0 ];then
 echo "该PID不存在！！"
 exit
fi
echo -e "\e[32m--------------------------------\e[0m"
echo "进程PID: ${P}"
echo "进程命令：$(ps -aux| awk '$2~/^'$P'$/{for (i=11;i<=NF;i++) printf("%s ",$i)}')"
echo "进程所属用户: $(ps -aux| awk '$2~/^'$P'$/{print $1}')"
echo "CPU占用率：$(ps -aux| awk '$2~/^'$P'$/{print $3}')%"
echo "内存占用率：$(ps -aux| awk '$2~/^'$P'$/{print $4}')%"
echo "进程开始运行的时间：$(ps -aux| awk '$2~/^'$P'$/{print $9}')"
echo "进程运行的时间：$(ps -aux| awk '$2~/^'$P'$/{print $10}')"
echo "进程状态：$(ps -aux| awk '$2~/^'$P'$/{print $8}')"
echo "进程虚拟内存：$(ps -aux| awk '$2~/^'$P'$/{print $5}')"
echo "进程共享内存：$(ps -aux| awk '$2~/^'$P'$/{print $6}')"
echo -e "\e[32m--------------------------------\e[0m"

```

### 根据进程名显示该进程所有信息

根据输入的程序的名字模糊过滤出所对应的 PID，并显示出详细信息，如果有多个PID，则全部显示

```shell
#! /bin/bash

read -p "请输入要查询的进程名：" NAME

N=`ps -aux | grep $NAME | grep -v grep | wc -l` ##统计进程总数

if [ $N -le 0 ];then
  echo "该进程名没有运行！"
fi
i=1
while [ $N -gt 0 ]
do
  echo -e "\e[32m***************************************************************\e[0m"
  echo "进程PID: $(ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $2}')"
  echo "进程命令：$(ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{for (j=11;j<=NF;j++) printf("%s ",$j)}')"
  echo "进程所属用户: $(ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $1}')"
  echo "CPU占用率：$(ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $3}')%"
  echo "内存占用率：$(ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $4}')%"
  echo "进程开始运行的时间：$(ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $9}')"
  echo "进程运行的时间：$(ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $10}')"
  echo "进程状态：$(ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $8}')"
  echo "进程虚拟内存：$(ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $5}')"
  echo "进程共享内存：$(ps -aux | grep $NAME | grep -v grep | awk 'NR=='$i'{print $0}'| awk '{print $6}')"
  echo -e "\e[32m***************************************************************\e[0m"
  let N-- i++
do```

### 根据用户名查看该用户的相关信息

``` shell
#! /bin/bash

read -p "请输入要查询的用户名：" name

echo "------------------------------"

n=`cat /etc/passwd | awk -F: '$1~/^'${name}'$/{print}' | wc -l`

if [ $n -eq 0 ];then
echo -e "\e[31m该用户不存在！\e[0m"
echo "------------------------------"
else
  echo "该用户的用户名：${name}"
  echo "该用户的UID：$(cat /etc/passwd | awk -F: '$1~/^'${name}'$/{print}'|awk -F: '{print $3}')"
  echo "该用户的组为：$(id ${name} | awk {'print $3'})"
  echo "该用户的GID为：$(cat /etc/passwd | awk -F: '$1~/^'${name}'$/{print}'|awk -F: '{print $4}')"
  echo "该用户的家目录为：$(cat /etc/passwd | awk -F: '$1~/^'${name}'$/{print}'|awk -F: '{print $6}')"
  Login=$(cat /etc/passwd | awk -F: '$1~/^'${name}'$/{print}'|awk -F: '{print $7}')
  if [ ${Login} == "/bin/bash" ];then
  echo -e "\e[32m该用户有登录系统的权限\e[0m"
  echo "------------------------------"
  elif [ ${Login} == "/sbin/nologin" ];then
  echo -e "\e[31m该用户没有登录系统的权限！\e[0m"
  echo "------------------------------"
  fi
fi

```