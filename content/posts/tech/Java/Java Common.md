---
title: "Java常用速记"
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



树状图
```
//将list，根据省市区信息分开，展示为树状图    
Map<String, List<Station>> lists = stations.stream().filter(x -> x.getProvince() != null && x.getCity() != null).collect(Collectors.groupingBy(Station::getProvince, LinkedHashMap::new, Collectors.toCollection(ArrayList::new)));
        //返回格式
        List<StationTreeVO> provinceList = new ArrayList<>();
        //遍历
        for (Map.Entry<String, List<Station>> entry : lists.entrySet()) {
            StationTreeVO province = new StationTreeVO();
            //获取list集合
            List<Station> value = entry.getValue();
            province.setProvince(entry.getKey());
            List<StationTreeVO.City> cityList = new ArrayList<>();
            //保持原有顺序-（LinkedHashMap按插曲顺序排序）
            Map<String, List<Station>> cityStreamList = value.stream().collect(Collectors.groupingBy(Station::getCity, LinkedHashMap::new, Collectors.toCollection(ArrayList::new)));
            for (Map.Entry<String, List<Station>> cityEntry : cityStreamList.entrySet()) {
                StationTreeVO.City city = new StationTreeVO.City();
                List<StationTreeVO.CityArea> areaList = new ArrayList<>();
                city.setCity(cityEntry.getKey());
                //获取list集合
                List<Station> areaValue = cityEntry.getValue();
                Map<String, List<Station>> areaStreamList = areaValue.stream().collect(Collectors.groupingBy(Station::getArea, LinkedHashMap::new, Collectors.toCollection(ArrayList::new)));
                for (Map.Entry<String, List<Station>> areaEntry : areaStreamList.entrySet()) {
                    StationTreeVO.CityArea area = new StationTreeVO.CityArea();
                    area.setArea(areaEntry.getKey());
                    List<Station> stationList = areaEntry.getValue();
                    area.setStationList(coverTree(stationList));
                    areaList.add(area);
                }
                city.setAreaList(areaList);
                cityList.add(city);
            }
            province.setCityList(cityList);
            provinceList.add(province);
        }

```

