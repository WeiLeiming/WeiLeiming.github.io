---
title: iOS App安装包瘦身
date: 2017-04-19 16:53:52
categories: iOS
tags:
	- iOS
---

# 背景

最近的一个小项目，打包出来的ipa包高达90.8MB，这是我始料未及的，于是动了心思为它瘦身。

<!--more-->

# 安装包瘦身

## 资源优化

资源包括图片、音视频文件、配置文件、字体包等。

### 删除无用资源及重复资源

无用资源指资源在工程文件中，但是没有被代码引用。重复资源指文件内容一样，文件名可以不一样的资源。

- 删除无用资源工具：[LSUnusedResources](https://github.com/tinymind/LSUnusedResources)
  - 勾选`Ignore similar name`防止误删
- 删除重复资源工具：[Gemini](https://macpaw.com/gemini)
  - 完全重复项可删，相似项看情况删除

一般来说工程管理的好的话，这两个是减少不了多少体积下来的。

### 资源压缩

- 图片资源压缩。
  - 工具：[TinyPng](https://tinypng.com/)或[ImageOptim](https://github.com/ImageOptim/ImageOptim)
- 音视频压缩。没做过。

资源压缩是一定要做的，可以缩小很多体积。

### 其他

- 图片资源尽量放在`Assets.xcassets`中管理。

## 编译优化

- `Build Settings -> Optimization Level`，Release设置为`Fastest, Smallest`。
- `Build Settings -> Strip Debug Symbols During Copy`设置为`Yes`。
- `Build Settings -> Symbols Hidden by Default`设置为`Yes`。
- `Build Settings -> Dead Code Stripping` 设置成 `Yes`。
- `Build Settings -> Strip Linked Product`设置为`Yes`。
- `Build Settings -> Deployment Postprocessing`设置为`Yes`。
- `Build Settings -> Use Separate Strip`设置为`Yes`。

编译优化感觉减小的并不多。

## 可执行文件优化

待研究

# 参考资料

[Build Setting Reference](https://developer.apple.com/library/mac/documentation/DeveloperTools/Reference/XcodeBuildSettingRef/1-Build_Setting_Reference/build_setting_ref.html)

[xcode build settings for debug symbol](http://jonathanblog2000.blogspot.sg/2013/07/xcode-build-settings-for-debug-symbol.html)

 [iOS IPA file size - xcode-archive vs. xcodebuild command](http://stackoverflow.com/questions/17650138/ios-ipa-file-size-xcode-archive-vs-xcodebuild-command)

[Xcode中和symbols有关的几个设置](http://www.jianshu.com/p/11710e7ab661)

[iOS可执行文件瘦身方法](http://www.cocoachina.com/ios/20150202/11084.html)

[iOS APP安装包瘦身实践](http://www.jianshu.com/p/c94dedef90b7)

[为自己的 APP 瘦身](http://superdanny.link/2017/03/03/App-Thinning/#more)

[iOS微信安装包瘦身](http://www.cocoachina.com/ios/20151203/14562.html)

[iOS9-by-Tutorials-学习笔记四：APP-瘦身](http://blog.csdn.net/mengxiangyue/article/details/50753858)