---
title: "Git常用"
date: 2022-09-28T00:14:01+08:00
lastmod: 2022-09-28T00:14:01+08:00
author: ["藏锋"]
keywords: 
- git
- tools
categories: 
- tools
tags: 
- tools
description: "Git常用的一些语句，语法及问题"
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

# Git操作
## 命令 
git push origin HEAD:refs/for/dev  推送到远端
- 改日志：
 git rebase -i
 git commit --amend -》修改里面日志信息，vim修改
 git rebase --continue

绑定本地分支与远端的分支，直接pull
 git branch --set-upstream-to=origin/work work
 
修改最新一次的提交
 git rebase -i dev~1
 pick修改为edit ，保存
 git add .
 git rebase --continue
 git push origin HEAD:refs/for/dev


---
### git强制覆盖本地仓库
`拉取所有更新，不同步；
``**git fetch --all**
`本地代码同步线上最新版本(会覆盖本地所有与远程仓库上同名的文件)；
`git reset --hard origin/master
`再更新一次（其实也可以不用，第二步命令做过了其实）
`git pull
`git fetch --all && git reset --hard origin/master && git pull


---

## 问题
ssh：no matching host key type found. Their offer: ssh-dss

在.ssh目录下创建config文件，并复制以下信息填入：

Host *
    HostKeyAlgorithms +ssh-rsa
    PubkeyAcceptedKeyTypes +ssh-rsa
    KexAlgorithms +diffie-hellman-group1-sha1


### 拉取问题
git github在内网环境设置代理访问报错：
fatal: unable to access 'https://github.com/XXX': Could not resolve host: github.com
Unable to fetch in submodule path ‘XXX'

$ git config --global http.proxy 127.0.0.1:10809
$ git config --global https.proxy https://10.xxx.xxx.xxx:1234
配置为代理，即可

## 配置项 

git config --global user.email "[gitHub](https://so.csdn.net/so/search?q=gitHub&spm=1001.2101.3001.7020)邮箱"

git config --global user.name "gitHub用户名"

git config--global --edit
```
[alias]
    s= status
   br = branch
   co = checkout
   df = diff
   amend = commit --amend --no-edit
   cs = commit -s
    u= add -u
   pub =    "!f() { git push origin HEAD:refs/for/$(gitrev-parse --abbrev-ref HEAD); }; f"
   sub = "!f() { git pull --rebase origin $(git rev-parse --abbrev-refHEAD); }; f"
   draft = "!f() { git push origin HEAD:refs/drafts/$(git rev-parse--abbrev-ref HEAD); }; f"
[user]
   name=xxx
   email=xxx@163.com
[core]
   editor=vi
   autocrlf=false
   safecrlf=false
[commit]
   template=D:\\xxx.template
[gui]
   encoding=utf-8
```


