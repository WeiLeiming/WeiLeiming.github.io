---
title: Swift中反向传值的方式
date: 2017-06-08 11:52:10
categories: iOS
tags:
	- iOS
	- Swift
---

最后更新时间：2017.6.30

在Swift中的反向传值方式有几种，如代理、通知、KVO、闭包(closure)等等。这里只要说说代理和闭包，Swift中的代理和闭包与Objective-C中代理和block反向传值的方式类似。

这里只是简单的步骤示例，做个记录，并未深究。

# 使用场景

从`B`往`A`传值是开发中必不可少的需求，如`View`往`ViewController`传值，`subViewController`往`ViewController`传值等。

<!-- more -->

# 闭包(closure)

闭包是自包含的函数代码块，可以在代码中被传递和使用。Swift中的闭包和C和Objective-C中的代码块(block)以及其他一些编程语言中的匿名函数比较相似。关于闭包的更多信息，查阅最后提供的[参考资料](#参考资料)。以下以一个在`View`中的`UIButton`点击事件传值给`ViewController`举例。

1. 在`B`中为closure声明一个类型别名

   ```swift
   typealias ClosureType = (UIButton) -> Void
   ```

2. 在`B`中定义一个closure变量

   ```swift
   var closure: ClosureType?
   ```

3. 在`B`中需要传值的地方调用

   ```swift
   self.closure?(sender)
   ```

4. 在`A`中写接受传值

   ```swift
   let b = B()
   b.closure = {(sender: UIButton) -> Void in
     	// xxx
   }
   ```

# 代理(delegate)

代理可能有点绕，熟悉后就很简单。举个例子，`B`要传值给`A`，那么`B`就拥有代理属性，`A`就是`B`的代理。

1. 在`B`中声明协议，最新已不需要继承`NSObjectProtocol`

   ```swift
   protocol CellDelegate: NSObjectProtocol {
       func cell(_ cell: Cell, sender: UIButton)
   }
   ```

2. 在`B`中定义代理属性，最新已不需要`weak`

   ```swift
   weak var delegate: CellDelegate?
   ```

3. 在`B`中调用代理方法

   ```swift
   self.delegate?.cell(self, sender: sender)
   ```

4. 在`A`中实现代理方法

   ```swift
   let b = B()
   b.delegate = self
   // MARK: - CellDelegate
   func cell(_ cell: Cell, sender: UIButton) {
   	// xxx
   }
   ```

# 参考资料

[Closures](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Closures.html#//apple_ref/doc/uid/TP40014097-CH11-ID94)

[Protocols](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/Protocols.html#//apple_ref/doc/uid/TP40014097-CH25-ID267)