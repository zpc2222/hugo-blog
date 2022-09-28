---
title: "maven"
date: 2022-09-05T00:17:58+08:00
lastmod: 2022-09-05T00:17:58+08:00
author: ["藏锋"]
keywords: 
- jvm
categories: 
- tech
tags: 
- java
- tech
description: "maven使用命令"
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

 将本地的jar上传至私服：
```shell
mvn deploy:deploy-file --settings=D:\java\apache-maven-3.8.6\conf\settings.xml  -DgroupId=cmbc.sdk  -DartifactId=SADK-CMBC  -Dversion=3.1.0.8-SNAPSHOT  -Dpackaging=jar  -Dfile=D:\javaDemo\lib\SADK-CMBC-3.1.0.8.jar -DrepositoryId=releases  -Durl=http://192.168.1.1:8081/repository/maven-snapshots
```


推送到仓库
mvn deploy:deploy-file -Dfile=xxx-web\target\radp-web-1.0.jar -DgroupId=com.richisland.radp -DartifactId=radp-web -Dversion=1.0 -Dpackaging=jar -DrepositoryId=fd-release –Durl=[http://xxx.com/repository/release/](http://xxx.com/repository/release/)

推送zip包

mvn deploy:deploy-file -Dfile=package.zip -DgroupId=xxx -DartifactId=package -Dversion=1.1 -Dpackaging=zip -DrepositoryId=fd-release –Durl=http://xxx.com/repository/release/

流水线推送

mvn deploy:deploy-file -Dfile=E:\package.tar.gz -DgroupId=xxx -DartifactId=pipelining -Dversion=1.1 -Dpackaging=gz -DrepositoryId=fd-release –Durl=http://xxx.com/repository/release/