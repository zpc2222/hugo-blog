---
title: "cron表达式"
date: 2022-09-05T00:17:58+08:00
lastmod: 2022-09-05T00:17:58+08:00
author: ["藏锋"]
keywords: 
- jvm
categories: 
- tech
tags: 
- java
- cron
description: "cron表达式"
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
# cron表达式
 （cron = "* * * * * *")  

## cron表达式格式：

{秒数} {分钟} {小时} {日期} {月份} {星期} {年份(可为空)}

例  "0 0 12 ? * WED" 在每星期三下午12:00 执行（年份通常 省略）
 0 0/10 * * * ? 10分钟一次
 */5 * * * * ? 每隔5秒执行一次
 0 */1 * * * ? 每隔1分钟执行一次
 0 0 5-15 * * ? 每天5-15点整点触发
 0 0/3 * * * ? 每三分钟触发一次
 0 0-5 14 * * ? 在每天下午2点到下午2:05期间的每1分钟触发 
 0 0/5 14 * * ? 在每天下午2点到下午2:55期间的每5分钟触发
 0 0/5 14,18 * * ? 在每天下午2点到2:55期间和下午6点到6:55期间的每5分钟触发
 0 0/30 9-17 * * ? 朝九晚五工作时间内每半小时
 0 0 10,14,16 * * ? 每天上午10点，下午2点，4点 
 
 0 0 12 ? * WED 表示每个星期三中午12点
 0 0 17 ? * TUES,THUR,SAT 每周二、四、六下午五点
 0 10,44 14 ? 3 WED 每年三月的星期三的下午2:10和2:44触发 
 0 15 10 ? * MON-FRI 周一至周五的上午10:15触发
 0 0 23 L * ? 每月最后一天23点执行一次
 0 15 10 L * ? 每月最后一日的上午10:15触发 
 0 15 10 ? * 6L 每月的最后一个星期五上午10:15触发 
 0 15 10 * * ? 2005 2005年的每天上午10:15触发 
 0 15 10 ? * 6L 2002-2005 2002年至2005年的每月的最后一个星期五上午10:15触发 
 0 15 10 ? * 6#3 每月的第三个星期五上午10:15触发


"30 * * * * ?" 每半分钟触发任务
"30 10 * * * ?" 每小时的10分30秒触发任务
"30 10 1 * * ?" 每天1点10分30秒触发任务
"30 10 1 20 * ?" 每月20号1点10分30秒触发任务
"30 10 1 20 10 ? *" 每年10月20号1点10分30秒触发任务
"30 10 1 20 10 ? 2011" 2011年10月20号1点10分30秒触发任务
"30 10 1 ? 10 * 2011" 2011年10月每天1点10分30秒触发任务
"30 10 1 ? 10 SUN 2011" 2011年10月每周日1点10分30秒触发任务
"15,30,45 * * * * ?" 每15秒，30秒，45秒时触发任务
"15-45 * * * * ?" 15到45秒内，每秒都触发任务
"15/5 * * * * ?" 每分钟的每15秒开始触发，每隔5秒触发一次
"15-30/5 * * * * ?" 每分钟的15秒到30秒之间开始触发，每隔5秒触发一次
"0 0/3 * * * ?" 每小时的第0分0秒开始，每三分钟触发一次
"0 15 10 ? * MON-FRI" 星期一到星期五的10点15分0秒触发任务
"0 15 10 L * ?" 每个月最后一天的10点15分0秒触发任务
"0 15 10 LW * ?" 每个月最后一个工作日的10点15分0秒触发任务
"0 15 10 ? * 5L" 每个月最后一个星期四的10点15分0秒触发任务
"0 15 10 ? * 5#3" 每个月第三周的星期四的10点15分0秒触发任务
