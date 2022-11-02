---
title: "springboot接入微信支付"
date: 2022-11-02T00:13:13+08:00
lastmod: 2022-11-02T00:13:13+08:00
author: ["藏锋"]
keywords: 
- java
categories: 
- tech
tags: 
- java
- map
description: "Java常用的一些小技巧"
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


## Map

### List中的实体类按照某个字段进行分组并存放至Map中操作
java8之前：
```
public static void main(String[] args) {
 List<User> list = new ArrayList<>();
 list.add(new User(1, 1));
 list.add(new User(1, 2));
 list.add(new User(2, 1));
 list.add(new User(2, 3));
 list.add(new User(2, 2));
 list.add(new User(3, 1));
 Map<Integer, List<User>> map = new HashMap<>();
 for(User user : list){
 if(map.containsKey(user.getId())){//map中存在此id，将数据存放当前key的map中
 map.get(user.getId()).add(user);
 }else{//map中不存在，新建key，用来存放数据
 List<User> tmpList = new ArrayList<>();
 tmpList.add(user);
 map.put(user.getId(), tmpList);
 }
 }
 System.out.println(map.toString());
 }
```
Java8后：
```
Map<Integer, List<MobildUserEntity>> map = userList.stream().collect(Collectors.groupingBy(MobildUserEntity::getStatus));

```




## List
### ArrayList

