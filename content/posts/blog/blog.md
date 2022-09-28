---
title: "第一篇博客"
date: 2022-09-27T20:01:00+08:00
lastmod: 2022-09-27T20:01:00+08:00
author: ["藏锋"]
keywords: 
- 博客
categories: 
- 博客
tags: 
- 博客
description: "用hugo写的第一篇Markdown格式的博客"
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
    image: "img/wanglian.jpg" #图片路径例如：posts/tech/123/123.png
    caption: "" #图片底部描述
    alt: ""
    relative: false
---



# 1、Markdown的编辑
## 1.1 新增md文件
开头要加上如下：

```
---
title: "{{ replace .Name "-" " " | title }}" #标题
date: {{ .Date }} #创建时间
lastmod: {{ .Date }} #更新时间
author: ["Sulv"] #作者
categories: 
- 分类1
- 分类2
tags: 
- 标签1
- 标签2
description: "" #描述
weight: # 输入1可以顶置文章，用来给文章展示排序，不填就默认按时间排序
slug: ""
draft: false # 是否为草稿
comments: true #是否展示评论
showToc: true # 显示目录
TocOpen: true # 自动展开目录
hidemeta: false # 是否隐藏文章的元信息，如发布日期、作者等
disableShare: true # 底部不显示分享栏
showbreadcrumbs: true #顶部显示当前路径
cover:
    image: "" #图片路径：posts/tech/文章1/picture.png
    caption: "" #图片底部描述
    alt: ""
    relative: false
---

```

### 1.1.1配置说明
>首先修改 frontmatter
>其中title表示文章标题，
>date为生成文章当时的时间，
>tags为标签，
>categories为目录，
>toc enable为启用文章目录（需要自己在文章中生成），
>description为文章摘要，
>draft表示是否为草稿（写完了文章把这里改为 false 即可），
><!--more-->为 LoveIt 主题的摘要标识符，该标识符上方的内容为文章摘要，如果上方为空，则采用 frontmatter 中设置的descriptions为文章摘要。


写完了文章进行网页的构建
>hugo serve -D -e production

-D表示草稿也要渲染，-serve表示启动一个本地服务器，即时渲染，方便修改。
hugo serve 的默认运行环境是 development, 而 hugo 的默认运行环境是 production。
由于本地 development 环境的限制, 评论系统**, **CDN 和 fingerprint 不会在 development 环境下启用。
你可以使用 hugo serve -e production 命令来开启这些特性。
值得一提的是不论输入的是server还是serve都是一样的。

在浏览器中前往它给出的 http://localhost:1313 就能看到你刚生成的博客了。

当你运行 hugo serve 时, 当文件内容更改时, 页面会随着更改自动刷新.

现在再输入指令

>hugo -D

这会生成一个 public 目录, 其中包含你网站的所有静态内容和资源. 现在可以将其部署在任何 Web 服务器上。

确认无误后就要把它发到公网上了，这里采用 GitHub pages 进行部署（当然，也有很多种方法也能达成这一目的）
## 1.2 GitHub pages部署
如果你是第一次使用 GitHub，请自行搜索如何配置，这里不做讲解！

首先确保你有一个 GitHub 账号，然后新建一个仓库，名为yourname.github.io，注意，你应该保证这里的 your name 为你的 GitHub 账号名称！然后再进行以下步骤：
 ```shell
cd public
git init
git remote add origin https://github.com/yourname/yourname.github.io.git
#此URL可在你的repo中找到
git add .
git commit -m "update %date%,%time%"
git push origin master

 ```


### 1.2.3自动推送脚本：
```bat
hugo -D
cd public
git add .
git commit -m "update %date%,%time%"
git push origin master
pause

```
#### 1.2.3.1 GitHub自动构建：
点击`Actions`选择`simple workflow`，内容如下
```
name: CI #自动化的名称
on:
  push: # push的时候触发
    branches: # 那些分支需要触发
      - master
jobs:
  build:
    runs-on: ubuntu-latest # 镜像市场
    steps:
      - name: checkout # 步骤的名称
        uses: actions/checkout@v2.3.4 #软件市场的名称
        with: # 参数
          submodules: true
      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2.4.13
        with:
          hugo-version: 0.91.2
          extended: true
      - name: Build
        run: hugo -D
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          deploy_key: ${{ secrets.ACTIONS_DEPLOY_KEY }}
          EXTERNAL_REPOSITORY: xxx/xxx.github.io # 注意要修改本处地址
          PUBLISH_BRANCH: master
          PUBLISH_DIR: ./public

```
值得注意的是在最后一条`Deploy`中应使用`with`而非`env`，应使用`deploy_key`而非其他的名字。但目前网上大部分教程都没提及这一点，甚至有的还错误地使用！

