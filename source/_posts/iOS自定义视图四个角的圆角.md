---
title: iOS自定义视图四个角的圆角
date: 2017-02-16 16:13:53
tags:
	- iOS
---

最近有一个需求：一个视图下面两个角需要圆角，而上面两个角不需要。

可以使用UIBezierPath和CAShapeLayer结合实现

```objective-c
+ (UIBezierPath *)bezierPathWithRoundedRect:(CGRect)rect byRoundingCorners:(UIRectCorner)corners cornerRadii:(CGSize)cornerRadii;
```
参数corners指定了想要需要成为圆角的角。可选值为：
```objective-c
typedef NS_OPTIONS(NSUInteger, UIRectCorner) {
    UIRectCornerTopLeft     = 1 << 0,
    UIRectCornerTopRight    = 1 << 1,
    UIRectCornerBottomLeft  = 1 << 2,
    UIRectCornerBottomRight = 1 << 3,
    UIRectCornerAllCorners  = ~0UL
};
```
<!-- more -->

使用示例：

```objective-c
UIView *view = [[UIView alloc] initWithFrame:CGRectMake(10, 10, 50, 50)];
view.backgroundColor = [UIColor redColor];
[self.view addSubview:view];
    
UIBezierPath *maskPath = [UIBezierPath bezierPathWithRoundedRect:view.bounds byRoundingCorners:UIRectCornerBottomLeft | UIRectCornerBottomRight cornerRadii:CGSizeMake(10, 10)];
CAShapeLayer *maskLayer = [[CAShapeLayer alloc] init];
maskLayer.frame = view.bounds;
maskLayer.path = maskPath.CGPath;
view.layer.mask = maskLayer;
```
如果需要将UIView的4个角全部都为圆角，做法相当简单，只需设置其Layer的cornerRadius属性即可。