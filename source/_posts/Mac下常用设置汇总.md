---
title: Mac下常用设置汇总
date: 2018-05-10 09:18:25
categories: 奇淫巧技
tags:
	- 奇淫巧技
	- Mac
---

# Mac下常用设置汇总

## 显示安全性与隐私里面无任何来源

```shell
$ sudo spctl --master-disable
```

## 隐藏文件显示隐藏设置

显示：

```shell
$ defaults write com.apple.finder AppleShowAllFiles YES
```

隐藏：

```shell
$ defaults write com.apple.finder AppleShowAllFiles NO
```

另一个更快的方式是快捷键：`Command + Shift + .`

<!-- more -->

## 修改终端下的默认HostName

```shell
$ sudo scutil --set HostName [新hostname]
```

