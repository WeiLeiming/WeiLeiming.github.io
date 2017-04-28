---
title: CocoaPods使用备忘录
date: 2017-02-17 10:07:32
tags:
	- iOS
---

1. CocoaPods 安装

如果网络已经翻墙，在终端中运行如下命令直接安装：

```
sudo gem install cocoapods
```
<!-- more -->

如果没翻墙，可以通过国内 Ruby China 或淘宝的 RubyGems 镜像进行安装。
在终端依次运行以下命令：
移除ruby的原生镜像

```
gem sources --remove https://rubygems.org/
```
用淘宝的镜像代替（淘宝的[镜像源](https://ruby.taobao.org/)停止更新了，请使用**Ruby China**的[镜像](https://gems.ruby-china.org/))

```
gem sources -a https://ruby.taobao.org/
```
用Ruby China的镜像代替
```
gem sources --add https://gems.ruby-china.org/
```
查看是否设置成功
```
gem sources -l

如果输出结果是如下这样，那说明这一步就成功了。
*** CURRENT SOURCES ***

https://gems.ruby-china.org/
```
安装Cocoapods
```
sudo gem install cocoapods
```
2. 其他一些命令
   查询Cocoapods是否支持某个类库
```
pod search 类库名
```
这个命令搜索的是本机上的最新版本，并没有联网查询。如果运行以上命令，没有搜到或者搜不到最新版本，可以运行以下命令，更新一下本地的 CocoaPods 源列表
```
pod repo update
```
打开终端，进入到工程目录，执行以下命令，会自动生成一个 Podfile 文件
```
pod init
```
安装框架
```
pod install
```
更新框架
```
pod update
或
# 禁止升级 CocoaPods 的 spec 仓库，否则会卡在 Analyzing dependencies，非常慢
pod update --verbose --no-repo-update
```
如果提示找不到库，则可去掉`--no-repo-update`。

在CocoaPods 中，有几种设置类库版本的方法。如：

\>= 1.10.X 会根据本地的 CocoaPods 源列表，导入不低于 1.10.X 版本的类库。

~> 1.10.X 会根据本地的 CocoaPods 源列表，介于 1.10.X~1.11.0 之前版本的类库。 建议锁定版本，便于团队开发。如，指定 1.10.0 版本

```
pod '类库名', '1.10.0'
```
清除 Cocoapods 本地缓存
特殊情况下，由于网络或者别的原因，通过 CocoaPods 下载的文件可能会有问题。
这时候可以删除 CocoaPods 的缓存(~/Library/Caches/CocoaPods/Pods/Release 目录)，再次导入即可。

关于 CocoaPods 的更多内容，可以参考 [CocoaPods 文档](https://cocoapods.org/)。