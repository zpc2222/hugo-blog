---
title: "部署hugo问题"
date: 2022-09-28T14:01:00+08:00
lastmod: 2022-09-28T20:14:07+08:00
author: ["藏锋"]
keywords: 
- 博客
categories: 
- 博客
tags: 
- blog
description: "用hugo发布静态博客遇到的一些问题"
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



# 关于 integrity 的错误
## 错误描述

在 Github Pages 上部署 Hugo 博客后，网站样式丢失，打开浏览器 F12 控制台可以发现错误：`Failed to find a valid digest in the 'integrity' attribute for resource "xxx.css", The resource has been blocked.`。

## 解决办法

我用的是 `PaperMod` 主题，发现很多人使用hugo用这个主题，都会有这个问题，大部分的说明就是资源被墙了，然后用CDN加速，但是那么复杂，咱也不会啊，还在还有另一种解决办法（我是解决了，别人不一定，可以做个参考）

### 修改html 文件

给出的解决办法是将网站中代表每个模块首页的 `index.html` 文件里面的 `integrity` 属性的值置空，我试着将网站首页的 `index.html` 文件改了一下，确实可以解决问题。但这么多 `index.html` 文件，一个个手改，工作量太大，也会容易遗漏。另外，即便这次改好了，后面再次重新发布文章的时候，就又会将其覆盖，所以这个方法并不可行。

### 修改基类 html 文件

后面倒是在 [Satckoverflow](https://stackoverflow.com/questions/65040931/hugo-failed-to-find-a-valid-digest-in-the-integrity-attribute-for-resource "Stackoverflow") 上发现了一个类似的问题。下面有人回复说是需要将 `assets` 文件夹下的 `head.html` 中的 `integrity` 的值置空，就可以一劳永逸了，以后每次发布文章也不用担心被覆盖。可是按照他的路径我并没找到该文件，经过一番试错，最后在 `themes\PaperMod\layouts\partials` 文件夹下找到一个 `head.html` 文件，发现里面确实有 `integrity="{{ $stylesheet.Data.Integrity }}"` 这么一句代码，把它改为 `integrity=""` 然后重新发布，发现效果杠杠的~

一个后端，搜了一天多前端的问题，虽然还是不懂，但是问题好歹解决了，网站正常访问了！