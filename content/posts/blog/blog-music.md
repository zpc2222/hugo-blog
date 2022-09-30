---
title: "视听"
date: 2022-09-27T20:01:00+08:00
lastmod: 2022-09-27T20:01:00+08:00
author: ["藏锋"]
keywords: 
- 博客
categories: 
- 博客
tags: 
- 音乐
description: "听听音乐吧"
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


Hugo文章里插入图片，我是用的图片url外链方式，MD里的写法是：

　　![测试图片](https://zpc2222.github.io/img/guanggao.jpg)

　　Hugo文章里插入视频，我用的B站外链短代码，这个短代码教程网上一搜就有了。

　　Hugo文章里插入音频，我还是用的url外链，写法：

<audio src="https://i.y.qq.com/v8/playsong.html?songid=256430&songtype=0#webchat_redirect" controls="controls" autoplay="autoplay">
</audio>  


<!-- cloud music -->
<!-- auto=1 可以控制自动播放与否，当值为 1 即打开网页就自动播放，值为 0 时需要访
客手动点击播放 -->
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=100% height=86 src="https://music.163.com/outchain/player?type=2&id=413812448&auto=1&height=66"></iframe>
![在这里插入图片描述](https://img-blog.csdnimg.cn/20200618103514921.png)




https://blog.csdn.net/ds19991999/article/details/81293467


写好了`bilibili.html`中，在markdown里嵌入视频的话，用以下形式写就ok了：
{{< bilibili BV1RE411b71v >}}

如果需要指定part
{{< bilibili BV18t41187Bx 3 >}}

