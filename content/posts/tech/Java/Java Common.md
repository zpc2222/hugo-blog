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


## 对图片文件转为Base64
图片转为字符串进行保存或者传输
selectStatistics.put("basePhoto","data:image/jpeg;base64,"+
Base64.getMimeEncoder().encodeToString(Files.readAllBytes(Paths.get(basePhotoPath))));

## 反射调用

 这里通过反射调用方法,带入ScheduleConfigBO的参数
```java
            Class<?> clazz = Class.forName(config.getClassName());
            String className = lowerFirstCapse(clazz.getSimpleName());
            Object bean = ApplicationContextHelper.getBean(className);
            assert bean != null;
            Method methodArgs = ReflectionUtils.findMethod(bean.getClass(), EXECUTE_METHOD, TaskConfigVO.class);
            Method method = ReflectionUtils.findMethod(bean.getClass(), EXECUTE_METHOD);
            if (methodArgs != null) {
                log.debug("invoke method [{}] with args [{}]", EXECUTE_METHOD, config);
                ReflectionUtils.invokeMethod(methodArgs, bean, config);
            }

```

## 根据当前日期，获取到月初，和月底时间
```
Public Map<String,Date>splitMonth(DateyearMonth){
Map<String,Date>map=newHashMap<>();
Calendarcale=Calendar.getInstance();
cale.setTime(yearMonth);
cale.add(Calendar.MONTH,0);
cale.set(Calendar.DAY_OF_MONTH,1);
DatefirstDayOfMonth=cale.getTime();//当月第一天2022-03-01

Calendarca=Calendar.getInstance();
ca.set(Calendar.DAY_OF_MONTH,ca.getActualMaximum(Calendar.DAY_OF_MONTH));
//将小时至23
ca.set(Calendar.HOUR_OF_DAY,23);
//将分钟至59
ca.set(Calendar.MINUTE,59);
//将秒至59
ca.set(Calendar.SECOND,59);
//将毫秒至999
ca.set(Calendar.MILLISECOND,999);
DatelastDayOfMonth=ca.getTime();
map.put("startDate",firstDayOfMonth);
map.put("endDate",lastDayOfMonth);
returnmap;
}

privateintweekDay(Datedate){
Calendarcalendar=Calendar.getInstance();
calendar.setTime(date);
returncalendar.get(Calendar.DAY_OF_WEEK);
}
```

## 根据传入的参数，来对日期区间进行拆分，返回拆分后的日期List

```
for(inti=0;i<listWeek.size();i+=2){
List<Date>list=getBetweenDates(listWeek.get(i),listWeek.get(i+1));
}


/**
*根据传入的参数，来对日期区间进行拆分，返回拆分后的日期List
*
*@return
*@throwsParseException
*@authorzhoupengcheng2022-3-10
*@editor
*@editcont
*/
publicstaticList<Date>splitDate(DatesDate,DateeDate)throwsParseException{
List<Date>listWeekOrMonth=newArrayList<Date>();

CalendarsCalendar=Calendar.getInstance();
sCalendar.setFirstDayOfWeek(Calendar.MONDAY);
sCalendar.setTime(sDate);

CalendareCalendar=Calendar.getInstance();
eCalendar.setFirstDayOfWeek(Calendar.MONDAY);
eCalendar.setTime(eDate);
booleanbool=true;

while(sCalendar.getTime().getTime()<eCalendar.getTime().getTime()){
if(bool||sCalendar.get(Calendar.DAY_OF_WEEK)==2||sCalendar.get(Calendar.DAY_OF_WEEK)==1){
listWeekOrMonth.add(sCalendar.getTime());
bool=false;
}
sCalendar.add(Calendar.DAY_OF_MONTH,1);
}
listWeekOrMonth.add(eCalendar.getTime());
if(listWeekOrMonth.size()%2!=0){
listWeekOrMonth.add(eCalendar.getTime());
}

returnlistWeekOrMonth;
}

```