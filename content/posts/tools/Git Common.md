---
title: "Git使用"
date: 2023-03-14T00:00:00+08:00
lastmod: 2023-03-14T00:00:00+08:00
author: ["藏锋"]
keywords: 
- git
categories: 
- tech
tags: 
- util
- git
description: "Git日常使用及问题记录"
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

## 常用语法

### 文件添加及提交
```
$ git add test.txt	//添加
$ git commit -m "wrote a test file"	//提交
$ git commit -m "add 3 files."		//一次性提交多个文件
```

### 改日志
```
git rebase -i
git commit --amend -》修改里面日志信息，vim修改
git rebase --continue
git push origin HEAD:refs/for/dev
git remote prune origin
```

### 修改已提交文件 dev分支
```
git rebase -i dev~1 
git rebase -i HEAD~3  表示要修改当前版本的倒数第三次状态,如果你要修改哪个，就把那行的pick改成edit
pick修改为edit ，保存
git add .
git rebase --continue
git push origin HEAD:refs/for/dev
```


```
(1) git reset --soft commitid3
(2) git status 可以看到绿色的已经add过的文件（即commitid1和commitid2的改动）
(3) git commit -s #添加评论，保存退出后会生成change_id
(4) git log 可以看到已经有了change_id
(5) git push origin HEAD:refs/for/dev 工作分支

git stash

可用来暂存当前正在进行的工作， 比如想pull 最新代码， 又不想加新commit， 或者另外一种情况，为了fix 一个紧急的bug, 先stash, 使返回到自己上一个commit,

改完bug之后再stash pop, 继续原来的工作。
git stash pop
```
### git强制覆盖本地仓库
拉取所有更新，不同步；
 >git fetch --all

本地代码同步线上最新版本(会覆盖本地所有与远程仓库上同名的文件)；`

>git reset --hard origin/master

再更新一次（其实也可以不用，第二步命令做过了其实）`

>git pull

>git fetch --all && git reset --hard origin/master && git pull

- 绑定本地分支与远端的分支，直接pull
 ``` shell
 git branch --set-upstream-to=origin/work work
```


## 安装及配置


### 指定名称和邮箱
```
$ git config --global user.name "Your Name"
 
$ git config --global user.email "email@example.com"
```
如果用了--global选项，那么更改的配置文件就是位于你用户主目录的那个，以后你所有的项目都会默认使用这里配置的用户信息。如果要在某个特定的项目中使用其他名字或者电邮，只要去掉--global选项重新配置即可，新的设定保存在当前项目的./git/config文件里

### 克隆仓库
```
git clone git://github.com/schacon/grit.git

如果希望在克隆的时候，自己定义要新建的项目目录名称，可以在上面的命令最后指定：
$ git clone git://github.com/schacon/grit.git myGit
唯一的差别就是，现在新建的目录成了mygrit，其它的都和上边的一样。
```

### 检查当前文件状态：

``` 
$ git status
```
忽略某些文件：可以创建一个名为.gitignore的文件，列出要忽略的文件模式。

### 要查看文件更新哪些部分 
不加参数直接输入：
```crystal
$ git diff
```


### 版本控制
```
$ git log	//查看提交历史记录，从最近到最远，可以看到3次
$ git log --pretty=oneline	//加参，简洁查看
$ git reflog	//查看每一次修改历史
$ cat test.txt	//查看文件内容
$ git status	//查看工作区中文件当前状态
$ git reset --hard HEAD^（HEAD~100）（commit id）	//回退版本
$ git checkout -- test.txt	//丢弃工作区的修改，即撤销修改
$ git reset HEAD test.txt	//丢弃暂存区的修改（若已提交，则回退）
```
### 删除文件
```
$ rm test.txt
//直接删除
$ git rm test.txt
$ git commit -m "remove test.txt"
//删错了，恢复
$ git checkout -- test.txt
```

### 不常用命令
```
$ git tag v1.0	//打标签
$ git tag -a v0.1 -m "version 0.1 released" 1094adb //指定标签名和说明文字
$ git tag	//查看所有标签
//若是忘记打，则查找历史提交commit id ，再打上
$ git log --pretty=oneline --abbrev-commit
$ git tag v0.9 f52c633
$ git show v0.9		//查看标签详细信息
$ git tag -d v0.1	//删除标签
$ git push origin v1.0	//推送标签到远程
$ git push origin –tags	//一次性推送全部本地标签
//删除标签，（若已推送到远程，先从本地删除，从远程删除）
$ git tag -d v0.9
$ git push origin :refs/tags/v0.9 

$ git config --global alias.st status	//配置别名
$ git config --global alias.unstage 'reset HEAD'  //配置操作别名
$ git config --global alias.last 'log -1'	//显示最后一次提交信息
$ git last	//显示最近一次的提交
$git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"  //颜色
$ cat .git/config //查看每个仓库的git配置文件
$ cat .gitconfig  //查看当前用户的git配置文件

$ git checkout -- test.txt 取消对文件的修改
```

## Tips


### 全局配置文件及commit模板指定
git config --global --edit
```
[alias]
    s = status
    br = branch
    co = checkout
    df = diff
    amend = commit --amend --no-edit
    cs = commit -s
    u = add -u
    pub =    "!f() { git push origin HEAD:refs/for/$(git rev-parse --abbrev-ref HEAD); }; f"
    sub = "!f() { git pull --rebase origin $(git rev-parse --abbrev-ref HEAD); }; f"
    draft = "!f() { git push origin HEAD:refs/drafts/$(git rev-parse --abbrev-ref HEAD); }; f"
[user]
    name=Zpc
    email=zpc@test.com
[core]
    editor=vi
    autocrlf=false
    safecrlf=false
[commit]
    template=D:\\xxx.template
[gui]
    encoding=utf-8
```

### git github在内网环境设置代理访问报错：
fatal: unable to access 'https://github.com/XXX': Could not resolve host: github.com
Unable to fetch in submodule path ‘XXX'

解决：
``` shell
$ git config --global http.proxy 127.0.0.1:10809
$ git config --global https.proxy https://10.xxx.xxx.xxx:1234
```
配置为代理，即可

### Unable to negotiate with xxx.xxx.xxx.xxx port XX: no matching host key type found. Their offer: ssh-rsa,ssh-dss fatal: Could not read from remote repository.

> 前提： 在排除没有配置公钥的情况下。

1.  在Git的安装目录下 `Git > etc > ssh` 文件夹下找到 `ssh_config` 文件 。
2.  在文件末尾添加一下代码。

```bash
# 注意这里的 xxx.com 是没有 https:// 的
# 如 https://github.com/， 填写 github.com 即可（最后的斜杆也不能要）。
Host xxx.com
    HostkeyAlgorithms +ssh-rsa
    PubkeyAcceptedAlgorithms +ssh-rsa
```
