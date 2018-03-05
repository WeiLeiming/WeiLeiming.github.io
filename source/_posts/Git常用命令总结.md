---
title: Git常用命令总结
date: 2018-01-11 10:36:10
tags:
	- Git
---

配置个人信息

```shell
# 配置个人信息
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
```

克隆仓库

```shell
$ git clone -b dev git@gitlab.fx110.com:ios_fxchat/FXChatRepo.git
```

<!-- more -->

查看仓库状态

```shell
# 查看仓库当前状态
$ git status
```

提交修改

```shell
# 把文件提交到本地仓库
$ git commit -m "Add README.md"
```

创建并切换分支

```shell
# 创建并切换到dev分支
$ git checkout -b dev
```

删除本地分支

```shell
# 删除dev分支
$ git branch -d dev
```

将本地新建分支提交到远程仓库

```shell
# 提交本地dev分支作为远程的dev分支
$ git push origin dev:dev
```

删除远程分支

```shell
# 远程dev分支将被删除，本地还在
$ git push origin :dev
或
$ git push origin -d dev
```

显示远程仓库的详细信息

```shell
$ git remote show origin
```

删除无效的远程追踪分支

```shell
$ git remote prune origin
```

查看命令记录

```shell
$ git reflog
```

显示版本树

```shell
$ git log --oneline --graph --decorate --all
```

恢复项目的历史到某个时间点

```shell
$ git reset --hard <SHA>
```

