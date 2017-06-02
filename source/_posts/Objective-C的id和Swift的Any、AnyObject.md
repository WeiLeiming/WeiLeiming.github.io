---
title: Objective-C的id和Swift的Any、AnyObject
date: 2017-06-02 10:22:03
tags:
	- iOS
	- Swift
	- Objective-C
---

官方编程指南中的解释：
> id可以表示任意类类型的实例
>
> Any可以表示任意类型，甚至包括函数类型
>
> AnyObject可以表示任意类类型的实例

Swift 2将Objective-C中的`id`类型映射到Swift中的`AnyObject`类型，这通常只能容纳类类型的值。Swift 2还为AnyObject提供了一些桥接值类型的隐式转换，如`String`，`Array`，`Dictionary`，`Set`和一些数字，以方便原生Swift类型可以轻松与Cocoa API的`NSString`，`NSArray`，或来自`Foundation`的其他容器类一起使用。这些转换与其他语言不一致，使得很难正确理解什么可以用作AnyObject。

在Swift 3中，Objective-C中的`id`类型映射到Swift中的`Any`类型。