---
title: 'Swift: sizeof与MemoryLayout'
date: 2017-06-30 10:19:16
tags:
	- Swift
typora-copy-images-to: ipic
---

# sizeof

在`C`语言中，可能会经常与`sizeof`打交道，用来计算数据占用内存大小。在`C`中`sizeof`即可以作用于类型也可以作用于某个实际的变量，并返回其在内存中的尺寸`size_t`。

在`Swift 3`以前，也有`sizeof`，不过与`C`中的运算符不同，它经过了一层包装，变成了一个只接受类型的方法，而接受具体值的则为另一个方法: `sizeofValue`

```swift
func sizeof<T>(_: T.Type) -> Int
func sizeofValue<T>(_: T) -> Int
```

<!-- more -->

不过`sizeofValue`接受的虽然是具体值，但是返回的是这个值的实际大小，而不是其内容的大小。所以与`C`中用`sizeof`拿来计算数组内容在内存中占据的尺寸不一样：

```c
// C
char bytes[] = {1, 2, 3};
sizeof(bytes);          // 3

int bytes[] = {1, 2, 3};
sizeof(bytes);          // 12
```

```swift
// Swift
let bytes = [1, 2, 3]
sizeofValue(bytes)      // 8: 64位系统一个引用的长度
```

# MemoryLayout

在`Swift 3`中`sizeof`和`sizeofValue`相关的被另一个新的枚举所替代，那就是`MemoryLayout`。

## 基本使用方法

```swift
let a = 10
MemoryLayout<Int>.size              // 8
MemoryLayout.size(ofValue: a)       // 8
```

## 属性方法介绍

`MemoryLayout`有3个非常有用的属性及3个对应的`Value`方法，返回值都为`Int`类型。

### size

实例使用`size(ofValue: T)`

`T`占用连续内存的大小，单位是字节。类型的大小不包括任何动态分配或不合适的存储，当`T`是类类型时，`MemoryLayout<T>.size`是相同的，不论`T`中存储属性有多少。当使用`unsafe pointer`为`T`的多个实例分配内存时，使用多个该类型的`stride`而不是其`size`，这个涉及到内存对齐的问题，详见[stride](#stride)。

### stride

实例使用`stride(ofValue: T)`

当存储在连续存储器或`Array<T>`中时，从`T`的任意一个实例开始到下一个实例开始所占用的连续内存字节大小。

示例：

![FA7434C3-B586-4E6D-BCA7-EEE42460C837](https://ws2.sinaimg.cn/large/006tKfTcly1fh389yep0ij30dv03174a.jpg)

这是一个数组，里面有四个`T`类型元素，每个`T`元素的大小为`size`个字节，但是因为内存对齐的限制，每个`T`类型元素实际消耗的内存空间为`stride`个字节，`stride - size`个字节则为每个元素因为内存对齐而浪费的内存空间。

### alignment

实例使用`alignment(ofValue: T)`

`T`的默认内存对齐方式，单位为字节。许多计算机系统对基本数据类型的合法地址做出了一些限制，要求某种数据类型对象的地址必须是某个值**K**(通常是 2、4或者8)的倍数。这种对齐限制简化了形成处理器和内存系统之间接口的硬件设计。**对齐原则是任何K字节的基本对象的地址必须是K的倍数。**

`MemoryLayout.alignment`就代表着数据类型`T`的内存对齐原则。而且在64位系统下，最大的内存对齐原则是8字节。

## 基本数据类型的`MemoryLayout`

```swift
// 值类型
MemoryLayout<Int>.size                                      // 8
MemoryLayout<Int>.stride                                    // 8
MemoryLayout<Int>.alignment                                 // 8

MemoryLayout<String>.size                                   // 24
MemoryLayout<String>.stride                                 // 24
MemoryLayout<String>.alignment                              // 8

// 引用类型 T
MemoryLayout<T>.size                                        // 8
MemoryLayout<T>.stride                                      // 8
MemoryLayout<T>.alignment                                   // 8

// 指针类型
MemoryLayout<unsafeMutablePointer<T>>.size                  // 8
MemoryLayout<unsafeMutablePointer<T>>.stride                // 8
MemoryLayout<unsafeMutablePointer<T>>.alignment             // 8

MemoryLayout<unsafeMutableBufferPointer<T>>.size            // 16
MemoryLayout<unsafeMutableBufferPointer<T>>.stride          // 16
MemoryLayout<unsafeMutableBufferPointer<T>>.alignment       // 16
```

# 注意

以上所有都在64位系统中得到

# 参考资料

- [SE-0101: Reconfiguring `sizeof` and related functions into a unified `MemoryLayout` struct](https://github.com/apple/swift-evolution/blob/master/proposals/0101-standardizing-sizeof-naming.md)


- [SE-0136: Memory Layout of Values](https://github.com/apple/swift-evolution/blob/master/proposals/0136-memory-layout-of-values.md)

- [sizeof 和 sizeofValue](http://swifter.tips/sizeof-sizeofvalue/)

- [Swift 对象内存模型探究（一）](https://mp.weixin.qq.com/s/zIkB9KnAt1YPWGOOwyqY3Q)