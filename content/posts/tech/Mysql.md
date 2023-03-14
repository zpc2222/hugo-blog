---
title: "MySQL使用"
date: 2023-03-13T00:00:00+08:00
lastmod: 2023-03-13T00:00:00+08:00
author: ["藏锋"]
keywords: 
- mybatis
categories: 
- tech
tags: 
- java
- mybatis
description: "MySQL简单用法以及常见问题的解决"
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

## Java写入MySQL

主键已存在，则忽略，不存在则插入

```mysql
INSERT INTO `global_config`(`id`,`name`,`description`,`value`,`status`)
SELECT 3,"order_auto_cancel_time","订单超时自动取消时间:分钟","20",1
WHERE NOT EXISTS(SELECT * FROM global_config WHERE `name` = 'order_auto_cancel_time');
```
## 导入导出

导出指定的表
``` mysql
 mysqldump -uroot -p -t fdb --tables t_role_resource t_interface_resource> /docker-entrypoint-initdb.d/data_table.sql
```

## mysql8密码不兼容问题

```
docker exec -it mysql bash 
mysql -uroot -p 
use mysql 
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '12!@QWqw'; 
flush privileges;
```

## 内存溢出

```

前端页面长时间无响应，后台查看error日志发现报错：
Out of sort memory, consider increasing server sort buffer size。 
字面意思就是 sort内存溢出，考虑增加服务器的排序缓冲区(sort_buffer_size)大小。

进入docker的MySQL服务内： 

docker exec -it mysql 
mysql -uroot -p 
mysql> show variables like '%sort_buffer_size%';
+-------------------------+---------+ | Variable_name | Value | +-------------------------+---------+ |
innodb_sort_buffer_size | 1048576 | |
myisam_sort_buffer_size | 8388608 | | 
sort_buffer_size | 262144 | +-------------------------+---------+ 
3 rows in set (0.01 sec) 

这里的sort_buffer_size=262144 ，262144/1024=256,156k比较小，需要扩大点，到2M（16G内存可以扩这么大）
mysql> SET GLOBAL sort_buffer_size = 210241024; 
Query OK, 0 rows affected (0.00 sec) 
mysql> exit
```

