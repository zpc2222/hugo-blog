---
title: "JVM分析"
date: 2022-09-05T00:17:58+08:00
lastmod: 2023-03-14T00:17:58+08:00
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

# JVM分析

## 参数
``` text
-Xms、-Xmn
堆内存分配：
JVM初始分配的内存由-Xms指定，默认是物理内存的1/64
JVM最大分配的内存由-Xmx指定，默认是物理内存的1/4
默认空余堆内存小于40%时，JVM就会增大堆直到-Xmx的最大限制；空余堆内存大于70%时，JVM会减少堆直到 -Xms的最小限制。
因此服务器一般设置-Xms、-Xmx相等以避免在每次GC 后调整堆的大小。对象的堆内存由称为垃圾回收器的自动内存管理系统回收。
非堆内存分配：
JVM使用-XX:PermSize设置非堆内存初始值，默认是物理内存的1/64；
由XX:MaxPermSize设置最大非堆内存的大小，默认是物理内存的1/4。
-Xmn2G：设置年轻代大小为2G。
-XX:SurvivorRatio，设置年轻代中Eden区与Survivor区的比值。
```
## 命令

jinfo（查看/修改虚拟机参数）

jmap命令是一个可以输出所有内存中对象的工具，甚至可以将VM 中的heap，以二进制输出成文本。
	打印出某个java进程（使用pid）内存内的，所有‘对象’的情况（如：产生那些对象，及其数量）。
	jmap -J-d64 -heap 12105
	jmap -heap 19570    -heap 打印heap的概要信息，GC使用的算法，heap（堆）的配置及JVM堆内存的使用情况.
	jmap -histo 24107   打印每个class的实例数目,内存占用,类全名信息. VM的内部类名字开头会加上前缀 * 
	                                  如果live子参数加上后,只统计活的对象数量。
	jmap -histo pid>a.log 日志将其保存，在一段时间后，使用文本对比工具，可以对比出GC回收了哪些对象。
	
```
using parallel threads in the new generation.  ##新生代采用的是并行线程处理方式
 
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

