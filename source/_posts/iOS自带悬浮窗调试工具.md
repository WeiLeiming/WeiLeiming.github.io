---
title: iOS自带悬浮窗调试工具
date: 2017-07-13 16:42:27
tags:
	- iOS
typora-copy-images-to: ipic
---

`UIDebuggingInformationOverlay`是`UIWindow`的私有子类，可以帮助开发人员调试自己的iOS应用程序。

# 使用

添加代码后使用两根手指同时点击状态栏即可调出。

## Objective-C用法

在`AppDelegate.m`的`- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions`方法中加入以下代码即可在Debug模式下开启

```objective-c
#if DEBUG
id overlayClass = NSClassFromString(@"UIDebuggingInformationOverlay");
[overlayClass performSelector:NSSelectorFromString(@"prepareDebuggingOverlay")];
#endif
```
## Swift用法

在`AppDelegate.swift`的`func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplicationLaunchOptionsKey: Any]?) -> Bool`方法中添加以下代码

```swift
#if DEBUG
let overlayClass = NSClassFromString("UIDebuggingInformationOverlay") as? UIWindow.Type
_ = overlayClass?.perform(NSSelectorFromString("prepareDebuggingOverlay"))
#endif
```

<!-- more -->

# 功能

简单介绍下各个选项是干嘛的，具体使用试试就知道了，就不在赘述。

![FEE5F646-6A8F-4428-8C7B-F01C539FA9F0](https://ws3.sinaimg.cn/large/006tKfTcly1fhmwm5phj9j30e50cidhc.jpg)

- View Hierarchy: 查看View的层次关系和相关属性
- VC Hierarchy: 查看ViewController的层次关系和相关属性
- Ivar Explorer: 查看UIApplication的成员属性
- Measure: 测量组件大小
- Spec Compare: 设计图对比。需在`Info.plist`中添加`NSPhotoLibraryUsageDescription`权限