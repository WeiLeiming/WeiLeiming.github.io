---
title: Objective-C的id和Swift的Any、AnyObject
date: 2017-06-02 10:22:03
tags:
	- iOS
	- Swift
	- Objective-C
---

Objective-C的`id`我们都知道可以用来表示任意类类型的实例，那么Swift中的`Any`和`AnyObject`有啥区别呢，看看官方编程指南中的解释：
> Any可以表示任意类型，甚至包括函数类型
>
> AnyObject可以表示任意类类型的实例

Swift 2将Objective-C中的`id`类型映射到Swift中的`AnyObject`类型，这通常只能容纳类类型的值，而对于值类型则无能为力。不过Swift 2为AnyObject提供了一些桥接值类型的隐式转换，如`String`，`Array`，`Dictionary`，`Set`和一些数字，以方便原生Swift类型可以轻松与Cocoa的`NSString`，`NSArray`，或来自`Foundation`的其他容器类一起使用。

<!-- more -->

只使用 Swift 类型而不转为 Cocoa 类型，对性能的提升是有所帮助的，所以我们应该尽可能地使用原生的类型。

在Swift 3中，Objective-C中的`id`类型被映射到Swift中的`Any`类型。它可以描述任何类型的值，无论是类、枚举、结构体还是其他Swift类型。而对于`Dictionary`和`Set`，有一种新类型`AnyHashable`可以保存任何符合Swift `Hashable`协议的类型的值。

以下是映射关系：

| Objective-C    | Swift 2               | Swift 3            |
| -------------- | --------------------- | ------------------ |
| id             | AnyObject             | Any                |
| NSArray *      | [AnyObject]           | [Any]              |
| NSDictionary * | [NSObject: AnyObject] | [AnyHashable: Any] |
| NSSet *        | Set\<NSObject>        | Set\<AnyHashable>  |

总之，在Swift 3中你会发现使用`Any`更加得心应手。但是在实际使用中，使用 `Any` 和 `AnyObject` 并不是什么令人愉悦的事情，应该尽量尝试明确地指出确定的类型，而不是大量使用这两者。

