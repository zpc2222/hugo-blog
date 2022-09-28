语言：中文

> 该主题根据Hugo PaperMod主题修改而来: https://github.com/adityatelange/hugo-PaperMod

## 1. git clone 拉取代码

① 用`git clone`的方式拉取代码至桌面，此时会在桌面生成sulv-hugo-papermod目录

② 进入到sulv-hugo-papermod目录，输入`git submodule update --init`，表示拉取themes/hugo-PaperMod/下的子模块，里面放的是官方主题

## 2. 启动界面

③ 把目录定位到sulv-hugo-papermod下，在终端输入`hugo server -D`，在浏览器输入：localhost:1313 即可看到现成的博客模板。

## 3. 修改信息

模板内部有许多个人信息需要自己配置，请耐心修改完，可以参考建站教程：[https://www.sulvblog.cn/posts/blog/](https://www.sulvblog.cn/posts/blog/)

## 4. 可能遇到的问题

1. 有些使用者会部署到github，可能遇到跨系统的问题，如提示`LF will be replaced by CRLF in ******`，这时输入命令：`git config core.autocrlf false`，解决换行符自动转换的问题。