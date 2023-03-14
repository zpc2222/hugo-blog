---
title: "JVM分析"
date: 2022-09-05T00:17:58+08:00
lastmod: 2022-09-05T00:17:58+08:00
author: ["藏锋"]
keywords: 
- jvm
categories: 
- tech
tags: 
- java
- JVM
description: "JVM分析方法"
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

JVM分析
tag-placeholder 
#Java 
jmap命令是一个可以输出所有内存中对象的工具，甚至可以将VM 中的heap，以二进制输出成文本。
	打印出某个java进程（使用pid）内存内的，所有‘对象’的情况（如：产生那些对象，及其数量）。
	jmap -J-d64 -heap 12105
	jmap -heap 19570    -heap 打印heap的概要信息，GC使用的算法，heap（堆）的配置及JVM堆内存的使用情况.
```
using parallel threads in the new generation.  ##新生代采用的是并行线程处理方式

using thread-local object allocation.   

Concurrent Mark-Sweep GC   ##同步并行垃圾回收

 

Heap Configuration:  ##堆配置情况，也就是JVM参数配置的结果[平常说的tomcat配置JVM参数，就是在配置这些]

   MinHeapFreeRatio = 40 ##最小堆使用比例

   MaxHeapFreeRatio = 70 ##最大堆可用比例

   MaxHeapSize      = 2147483648 (2048.0MB) ##最大堆空间大小

   NewSize          = 268435456 (256.0MB) ##新生代分配大小

   MaxNewSize       = 268435456 (256.0MB) ##最大可新生代分配大小

   OldSize          = 5439488 (5.1875MB) ##老年代大小

   NewRatio         = 2  ##新生代比例

   SurvivorRatio    = 8 ##新生代与suvivor的比例

   PermSize         = 134217728 (128.0MB) ##perm区 永久代大小

   MaxPermSize      = 134217728 (128.0MB) ##最大可分配perm区 也就是永久代大小

 

Heap Usage: ##堆使用情况【堆内存实际的使用情况】

New Generation (Eden + 1 Survivor Space):  ##新生代（伊甸区Eden区 + 幸存区survior(1+2)空间）

   capacity = 241631232 (230.4375MB)  ##伊甸区容量

   used     = 77776272 (74.17323303222656MB) ##已经使用大小

   free     = 163854960 (156.26426696777344MB) ##剩余容量

   32.188004570534986% used ##使用比例

Eden Space:  ##伊甸区

   capacity = 214827008 (204.875MB) ##伊甸区容量

   used     = 74442288 (70.99369812011719MB) ##伊甸区使用

   free     = 140384720 (133.8813018798828MB) ##伊甸区当前剩余容量

   34.65220164496263% used ##伊甸区使用情况

From Space: ##survior1区

   capacity = 26804224 (25.5625MB) ##survior1区容量

   used     = 3333984 (3.179534912109375MB) ##surviror1区已使用情况

   free     = 23470240 (22.382965087890625MB) ##surviror1区剩余容量

   12.43827838477995% used ##survior1区使用比例

To Space: ##survior2 区

   capacity = 26804224 (25.5625MB) ##survior2区容量

   used     = 0 (0.0MB) ##survior2区已使用情况

   free     = 26804224 (25.5625MB) ##survior2区剩余容量

   0.0% used ## survior2区使用比例

PS Old  Generation: ##老年代使用情况

   capacity = 1879048192 (1792.0MB) ##老年代容量

   used     = 30847928 (29.41887664794922MB) ##老年代已使用容量

   free     = 1848200264 (1762.5811233520508MB) ##老年代剩余容量

   1.6416783843721663% used ##老年代使用比例

Perm Generation: ##永久代使用情况

   capacity = 134217728 (128.0MB) ##perm区容量

   used     = 47303016 (45.111671447753906MB) ##perm区已使用容量

   free     = 86914712 (82.8883285522461MB) ##perm区剩余容量

   35.24349331855774% used ##perm区使用比例
```


垃圾回收统计：jstat -gc 进程id
	S0C：第一个幸存区的大小
	S1C：第二个幸存区的大小
	S0U：第一个幸存区的使用大小
	S1U：第二个幸存区的使用大小
	EC：伊甸园区的大小
	EU：伊甸园区的使用大小
	 OC：老年代大小
	OU：老年代使用大小
	MC：方法区大小
	MU：方法区使用大小
	CCSC:压缩类空间大小
	CCSU:压缩类空间使用大小
	YGC：年轻代垃圾回收次数
	YGCT：年轻代垃圾回收消耗时间
	FGC：老年代垃圾回收次数
	FGCT：老年代垃圾回收消耗时间
	GCT：垃圾回收消耗总时间

堆内存统计：jstat -gccapacity 进程id
	NGCMN：新生代最小容量
	NGCMX：新生代最大容量
	NGC：当前新生代容量
	S0C：第一个幸存区大小
	S1C：第二个幸存区的大小
	EC：伊甸园区的大小
	OGCMN：老年代最小容量
	OGCMX：老年代最大容量
	OGC：当前老年代大小
	OC:当前老年代大小
	MCMN:最小元数据容量
	MCMX：最大元数据容量
	MC：当前元数据空间大小
	CCSMN：最小压缩类空间大小
	CCSMX：最大压缩类空间大小
	CCSC：当前压缩类空间大小
	YGC：年轻代gc次数
	FGC：老年代GC次数

