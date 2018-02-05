---
title: iPhone X适配
date: 2018-02-05 14:41:48
tags:
	- iOS
typora-copy-images-to: ipic
---

# 各种Bar在iPhone X的高度

以下均为竖屏状态

## statusBar

在 iPhone X 中状态栏的 frame 为

![16BF80A4-7E3A-49DF-9101-D576A2464C52](https://ws1.sinaimg.cn/large/006tKfTcgy1fo5lqbllxvj30bg00smx8.jpg)

即高度为`44 points`，比其他机型的20 points 多出了`24 points`。

<!-- more -->

## navigationBar

在 iPhone X 中导航栏的 frame 为

![29D70DF4-F46C-479E-9FDC-14BCFB4820CA](https://ws4.sinaimg.cn/large/006tKfTcgy1fo5mmainrpj30at00fa9z.jpg)

与其他机型是一致的，均为`44 points`。

## tabBar

在 iPhone X 中 UITabBar 的 frame 为

![2104736E-DCE5-4539-BB34-4EA60BB8C069](https://ws1.sinaimg.cn/large/006tKfTcgy1fo5meblfttj309q00e0sn.jpg)

即高度为`83 points`，比其他机型的49 points 多出了`34 points`。

# Safe Area

iOS11引入了 Safe Area 的概念，iPhone X 适配就是将我们的内容放置在 Safe Area 内

![9720C5AA-E093-4180-B5F3-4C9C8CAB54FD](https://ws2.sinaimg.cn/large/006tKfTcgy1fo5kvbcwuaj30fh0aa3yk.jpg)

在 iOS11中 UIView 新增了一个属性可以用来获取当前 view 的安全区域，该属性在 `viewWillAppear` 后方能获取到正确的值

```Swift
@available(iOS 11.0, *)
open var safeAreaInsets: UIEdgeInsets { get }
```

以上两种图的 `safeAreaInsets` 的值为

```swift
// 竖屏
UIEdgeInsets(top: 44.0, left: 0.0, bottom: 34.0, right: 0.0)
// 横屏
UIEdgeInsets(top: 0.0, left: 44.0, bottom: 21.0, right: 44.0)
```

如果想要更改 Safe Area 的范围，可通过 `UIViewController` 的 `additionalSafeAreaInsets` 更改

```swift
/* Custom container UIViewController subclasses can use this property to add to the overlay
that UIViewController calculates for the safeAreaInsets for contained view controllers.
*/
@available(iOS 11.0, *)
open var additionalSafeAreaInsets: UIEdgeInsets
```

当更改了 Safe Area，可通过以下两种方式获取到回调

```swift
UIView.safeAreaInsetsDidChange()
UIViewController.viewSafeAreaInsetsDidChange()
```

iOS11 废弃掉了 `topLayoutGuide` 和 `bottomLayoutGuide` ，`UIView` 新增了 `safeAreaLayoutGuide` 来做自动适配

```swift
/* The top of the safeAreaLayoutGuide indicates the unobscured top edge of the view (e.g, not behind
the status bar or navigation bar, if present). Similarly for the other edges.
*/
@available(iOS 11.0, *)
open var safeAreaLayoutGuide: UILayoutGuide { get }
```

# 适配

## AutoLayout

因为新增了 `safeAreaLayoutGuide` ，自动适配时会自动加上这些 `safeLayoutGuide`，因此不需修改什么

注意：在 xib 中请检查 `Use Safe Area Layout Guides` 是否勾选（默认为开启）

![6764DF39-09D1-42D6-B9E8-C41C09FCC4F8](https://ws4.sinaimg.cn/large/006tKfTcgy1fo5nxpglz6j307304vjrq.jpg)

## Frame

默认的 Safe Area 范围为

```swift
// 竖屏
UIEdgeInsets(top: 44.0, left: 0.0, bottom: 34.0, right: 0.0)
// 横屏
UIEdgeInsets(top: 0.0, left: 44.0, bottom: 21.0, right: 44.0)
```

竖屏适配规则为电池栏由原来的 `20 points` 改为 `44 points`，底部留白 `34 points`

横屏为左右各留白`44 points`，底部留白`21 points`

### 竖屏

`UIDevice` 新增检测 iPhone X 的方法

```swift
static func iPhoneX() -> Bool {
    if UIScreen.main.bounds.height == 812 {
        return true
    }
    return false
}
```

顶部带有 `navigationBar`

```swift
// kNavigationBarHeight
let kNavigationBarHeight: CGFloat = UIDevice.iPhoneX() ? 88 : 64
```

顶部如不带 `navigationBar`

```swift
// kSafeAreaTopInset
let kSafeAreaTopInset: CGFloat = UIDevice.iPhoneX() ? 44 : 20
```

底部如不带 `tabbar`

```swift
// kSafeAreaBottomInset
let kSafeAreaBottomInset: CGFloat = UIDevice.iPhoneX() ? 34 : 0
```

### 横屏

横屏类似，就不在赘述

# 汇信适配

- 如使用的 `AutoLayout`，请看 [AutoLayout](#AutoLayout)


- 顶部适配：已在 `UIViewController` 增添扩展属性方法 `fx_Y` 直接获取 Y 坐标

  返回规则：

  ```swift
  /// 有导航栏有透明度时：iPhone X为88，其他机型为64
  /// 有导航栏无透明度时：iPhone X为0，其他机型为0
  /// 无导航栏有状态栏时：iPhone X为44，其他机型为20
  /// 无导航栏无状态栏时：iPhone X为24，其他机型为0
  ```

  用法示例：

  ```swift
  CGRect sampleViewRect = CGRectMake(0,
                                     self.fx_Y,
                                     self.view.fx_width,
                                     self.view.fx_height - self.fx_Y - 34);
  ```


- 底部适配：设置底部距离下边界 `34 points`。

  如根据系统开启了以下两个属性

  ```swift
  // < iOS 11
  UIViewController: automaticallyAdjustsScrollViewInsets
  // > iOS 11
  UIScrollView: contentInsetAdjustmentBehavior
  ```

  则 `scrollView` 及其子类可以不用调整 Y 坐标，只需做底部适配。

  ​