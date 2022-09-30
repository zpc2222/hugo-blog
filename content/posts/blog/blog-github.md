---
title: "github部署静态博客"
date: 2022-09-30T12:01:00+08:00
lastmod: 2022-09-30T13:01:00+08:00
author: ["藏锋"]
keywords: 
- 博客
categories: 
- 博客
tags: 
- 博客
description: "使用hugo部署静态博客，并自动发布"
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



# 基于Hugo部署静态博客

`先给大家介绍下：
>hugo号称是**The world’s fastest framework for building websites**，是由go语言编写的，编译速度和运行速度都是杠杠的。不像hexo依赖于node.js，项目依赖模块多，hexo g生成网页也比较慢

`选择方面：`
># 静态博客框架jekyll、hexo和hugo三者
>-   Hugo ：51.5k [Github地址](https://link.zhihu.com/?target=https%3A//github.com/gohugoio/hugo)/[官网地址](https://link.zhihu.com/?target=https%3A//gohugo.io/)/[主题汇总1](https://link.zhihu.com/?target=https%3A//themes.gohugo.io/)/[主题汇总2](https://link.zhihu.com/?target=https%3A//www.gohugo.org/theme/)/[主题汇总3](https://link.zhihu.com/?target=https%3A//hugothemesfree.com/)
>-   Jekyll：42.6k [Github地址](https://link.zhihu.com/?target=https%3A//github.com/jekyll/jekyll)/[官网地址](https://link.zhihu.com/?target=https%3A//jekyllrb.com/)/[主题汇总1](https://link.zhihu.com/?target=http%3A//jekyllthemes.org/)/[主题汇总2](https://link.zhihu.com/?target=https%3A//jekyllthemes.io/free)/[主题汇总3](https://link.zhihu.com/?target=http%3A//themes.jekyllrc.org/)
>-   Hexo： 32.6k [Github地址](https://link.zhihu.com/?target=https%3A//github.com/hexojs/hexo)/[官网地址](https://link.zhihu.com/?target=https%3A//hexo.io/)/[主题汇总](https://link.zhihu.com/?target=https%3A//hexo.io/themes/)
>**Hugo使用go语言是一种编译型语言，速度非常快**，而Jekyll使用ruby编写，hexo使用nodejs编写
静态网站，hugo的next主题部署
>-   Jekyll 有github支持，可以将markdown文件直接放到git仓库，github会自动生成网页文件。（Github一直是一个亲ruby的社区）
>-   Hexo提供了方便的部署命令，可以做到一条命令部署到github上。
>-   Hugo的官方文档写的非常好，部署简洁。前两者部署时需要安装很多依赖，而hugo可以直接提供二进制文件运行，甚至不需要root权限。

>因为种种原因，本人最早用的Jekyll，后来发现Hexo主题更多，但是文章多的时候都比较慢（静态博客，每次都是全量生成的）,Hugo完美解决了这个问题，唯一的劣势就是主题不太多，但是程序员嘛，能用就行了！

下面开始正文
## 安装hugo环境
Hugo的安装有很多方式，[Install Hugo | Hugo (gohugo.io)](https://link.zhihu.com/?target=https%3A//gohugo.io/getting-started/installing/) ，根据个人喜好可以自行安装。

1.  [Releases · gohugoio/hugo (github.com)](https://link.zhihu.com/?target=https%3A//github.com/gohugoio/hugo/releases) 下载最新版本，建议下载最新的，[hugo_extended_0.104.2_windows-amd64.zip](https://link.zhihu.com/?target=https%3A//github.com/gohugoio/hugo/releases/download/v0.104.2/hugo_extended_0.104.2_windows-amd64.zip)
2.  环境变量配置
 下好之后找个空文件夹放了，比如我这里用的`D:\Program Files\hugo`
  修改系统的环境变量，将`hugo.exe`所在文件夹添加到里面
![](https://zpc-1306994356.cos.ap-nanjing.myqcloud.com/ob/202209301344890.png)

![](https://zpc-1306994356.cos.ap-nanjing.myqcloud.com/ob/202209301344510.png)

输入 hugo version 正确显示版本即可。

## 下载模板
1. 克隆模板
 git clone https://github.com/zpc2222/hugo-blog.git
2. 修改配置项config.yml
  这里不做详细解释，看里面直接修改就行了
3. 运行
>hugo serve -D

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
## GitHub pages部署
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


### 自动推送脚本：
```bat
hugo -D
cd public
git add .
git commit -m "update %date%,%time%"
git push origin master
pause

```
#### GitHub自动构建：
点击`Actions`选择`simple workflow`，内容如下
```
# This is a basic workflow to help you get started with Actions
 
name: hugo-deploy-CI
 
# Controls when the action will run. 
on: push
 
# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # docs：https://github.com/peaceiris/actions-gh-pages
  deploy:
    runs-on: ubuntu-20.04
    steps:
      - name: Git checkout
        uses: actions/checkout@v2
 
      - name: Setup hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: '0.104.0'
          extended: true
 
      - name: Build
        run: hugo
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          personal_token: ${{ secrets.PERSONAL_TOKEN }}
          external_repository: zpc2222/zpc2222.github.io # 修改为自己的地址
          publish_dir: ./public
          keep_files: true
          publish_branch: master

```
值得注意的是在最后一条`Deploy`中应使用`with`而非`env`，应使用`deploy_key`而非其他的名字。但目前网上大部分教程都没提及这一点，甚至有的还错误地使用！（使用的我模板可以跳过这步，在工程里直接修改EXTERNAL_REPOSITORY后的路径即可）

上面操作完成后，你会发现action还是会报错的，因为personal_token（个人令牌）你没有生成，需要在仓库内配置下，名字是PERSONAL_TOKEN，value则是个人令牌，最好不要设置有效期：
![](https://zpc-1306994356.cos.ap-nanjing.myqcloud.com/ob/202209301354306.png)

令牌如何生成，这里不做讲述
然后每次提交代码，本工程的定时任务，会将静态的页面自动发布到 youname.github.io功能内，这个工程会自动发布到gitpages。如果没有配置的，可以检查下分支是否正确，我默认配置的是master
![](https://zpc-1306994356.cos.ap-nanjing.myqcloud.com/ob/202209301356627.png)

圈红的注意。
