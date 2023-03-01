---
title: "音乐"
date: 2022-09-01T20:01:00+08:00
lastmod: 2022-10-02T20:01:00+08:00
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

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/aplayer@1.7.0/dist/APlayer.min.css">  
<script src="https://cdn.jsdelivr.net/npm/aplayer@1.7.0/dist/APlayer.min.js"></script>  
<script src="https://cdn.jsdelivr.net/npm/meting@1.1.0/dist/Meting.min.js"></script>

## 我的音乐歌单

<div class="aplayer" data-id="2384590275" data-server="tencent" data-type="playlist" data-mode="circulation" data-autopla="true"></div>
 
---

### 音乐插件
写入后，根据id，server自行修改，网易云，QQ音乐，酷狗都支持；目前非会员已经无法白嫖听歌了，只能听免费歌曲

```html

<link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/aplayer@1.7.0/dist/APlayer.min.css">  
<script src="https://cdn.jsdelivr.net/npm/aplayer@1.7.0/dist/APlayer.min.js"></script>  
<script src="https://cdn.jsdelivr.net/npm/meting@1.1.0/dist/Meting.min.js"></script>

<div class="aplayer" data-id="2384590275" data-server="tencent" data-type="playlist" data-mode="circulation" data-autopla="true"></div>
```

#### 参数说明
主要参数 | 值|
---|---|
data-id|歌曲/专辑/歌单 ID
data-server|netease（网易云音乐）tencent（QQ音乐） xiami（虾米） kugou（酷狗）
data-type|song （单曲） album （专辑） playlist （歌单） search （搜索）
data-mode|random （随机） single （单曲） circulation （列表循环） order （列表）
data-autoplay|false（手动播放） true（自动播放）

 ---
## 哔哩哔哩视频


写好了`bilibili.html`中，在markdown里嵌入视频的话，用以下形式写就ok了：
{{< bilibili BV1RE411b71v >}}

如果需要指定part
{{< bilibili BV18t41187Bx 3 >}}

