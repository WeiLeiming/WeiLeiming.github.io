---
title: iOS如何利用runtime向Category添加属性
date: 2017-02-05 14:56:14
tags:
	- iOS
	- runtime
	- Category
---

# Associated Objects

- 使用runtime需导入头文件

```objective-c
#import <objc/runtime.h>
```

- 以下方法允许对象在运行时通过键关联任意值

```objective-c
objc_getAssociatedObject(id object, const void *key);
objc_setAssociatedObject(id object, const void *key, id value, objc_AssociationPolicy policy);
```

<!-- more -->

## Associative Object Behaviors

- 在runtime.h中的定义

```objective-c
/**
 * Policies related to associative references.
 * These are options to objc_setAssociatedObject()
 */
typedef OBJC_ENUM(uintptr_t, objc_AssociationPolicy) {
    OBJC_ASSOCIATION_ASSIGN = 0,           /**< Specifies a weak reference to the associated object. */
    OBJC_ASSOCIATION_RETAIN_NONATOMIC = 1, /**< Specifies a strong reference to the associated object. 
                                            *   The association is not made atomically. */
    OBJC_ASSOCIATION_COPY_NONATOMIC = 3,   /**< Specifies that the associated object is copied. 
                                            *   The association is not made atomically. */
    OBJC_ASSOCIATION_RETAIN = 01401,       /**< Specifies a strong reference to the associated object.
                                            *   The association is made atomically. */
    OBJC_ASSOCIATION_COPY = 01403          /**< Specifies that the associated object is copied.
                                            *   The association is made atomically. */
};
```

- 根据枚举类型objc_AssociationPolicy定义的行为，可以将值关联到对象上

|             Behavior              |           @property Equivalent           |
| :-------------------------------: | :--------------------------------------: |
|      OBJC_ASSOCIATION_ASSIGN      | @property (assign) 或 @property (unsafe_unretained) |
| OBJC_ASSOCIATION_RETAIN_NONATOMIC |      @property (nonatomic, strong)       |
|  OBJC_ASSOCIATION_COPY_NONATOMIC  |       @property (nonatomic, copy)        |
|      OBJC_ASSOCIATION_RETAIN      |        @property (atomic, strong)        |
|       OBJC_ASSOCIATION_COPY       |         @property (atomic, copy)         |

## 使用示例

### NSObject+AssociatedObject.h

```objective-c
@interface NSObject (AssociatedObject)
@property (nonatomic, strong) id associatedObject;
@end
```

### NSObject+AssociatedObject.m

```objective-c
@implementation NSObject (AssociatedObject)
@dynamic associatedObject;

- (void)setAssociatedObject:(id)object {
     objc_setAssociatedObject(self, @selector(associatedObject), object, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
}

- (id)associatedObject {
    return objc_getAssociatedObject(self, @selector(associatedObject));
}
```

通常建议键是一个静态字符。

```objective-c
static char kAssociatedObjectKey;
objc_getAssociatedObject(self, &kAssociatedObjectKey);
```

当然，存在一个简单得多的解决方案：只需使用selector。





更详细的参考请访问[[Associated Objects](http://nshipster.com/associated-objects/)]