---
title: iOS面试记录
date: 2017-10-16 14:41:04
tags:
	- iOS
	- 面试
---

最后更新时间：2017.11.14

自己面试遇到或收集的一些面试题，在这里记录回答

<!-- more -->

### 父类实现深拷贝时，子类如何实现深度拷贝。父类没有实现深拷贝时，子类如何实现深度拷贝。

若想实现拷贝，则需遵从`NSCopying`协议（可变的是`NSMutableCopying`协议），实现该协议的方法

```objective-c
- (id)copyWithZone:(NSZone *)zone;
```

如果父类已实现深拷贝，子类只需重写`- (id)copyWithZone:(NSZone *)zone`，调用父类的`copyWithZone`，并对子类自己的属性进行处理即可。

父类如果没有实现深拷贝，那么不仅需要对子类的属性进行处理，还需要对父类的属性进行处理。

### KVO，NSNotification，delegate及block区别

KVO:键值观察，一对多，可以监听对象属性变化，只监听数据变化的时候常用

NSNotifiction:通知，一对多，相比于KVO多了一步发送通知的操作，但不局限于KVO只能监听属性，使用更加灵活

delegate:代理，一对一，比如两个类之间的传值，类A调用类B的方法，类B在执行过程中遇到问题通知类A，这时候我们可以用到代理

block:代码块，使用场景与delegate一致，能够直接访问上下文，使用block的地方和block的实现在同一个地方，比使用delegate时代码组织更连贯，更直观。回调比较多时使用delegate

### KVC如何实现，如何进行键值查找。KVO如何实现

- 调用`setValue:forKey:`：

  1. 检查有没有跟key相同名称的set方法，如果有，就会调用set方法

  2. 如果set方法不存在，类方法`accessInstanceVariablesDirectly`（是否直接访问没有生成访问器的变量）返回`YES`（这是`NSKeyValueCodingCatogery`中实现的类方法，默认实现为返回`YES`），那么按\_**key**，\_is**Key**，**key**，is**Key**的顺序查找成员属性，如果有，则直接给成员属性赋值

  3. 如果还没找到，则调用`setValue:forUndefinedKey:`方法（默认实现是抛出异常，根据需要重写）

- 调用`valueForKey:`：

  1. 按get**Key**，**key**，is**Key**的顺序查找getter方法，如果有，直接调用。如果是bool、int等内建值类型，会做NSNumber的转换
  2. 如果没有找到getter，查找countOf**Key**、objectIn**Key**AtIndex:、**key**AtIndexes格式的方法。如果countOf**Key**和另外两个方法中的一个找到，那么就会返回一个可以响应NSArray所有方法的代理集合(collection proxy object)。发送给这个代理集合(collection proxy object)的NSArray消息方法，就会以countOf**Key**、objectIn**Key**AtIndex:、**key**AtIndexes这几个方法组合的形式调用。还有一个可选的get**key**:range:方法
  3. 还没查到的话，那么查找countOf**Key**、enumeratorOf**Key**、memberOf**Key**:格式的方法。如果这三个方法都找到，那么就返回一个可以响应NSSet所有方法的代理集合(collection proxy object)。发送给这个代理集合(collection proxy object)的NSSet消息方法，就会以countOf**Key**、enumeratorOf**Key**、memberOf**Key**:组合的形式调用
  4. 还是没查到，那么如果类方法`accessInstanceVariablesDirectly`返回`YES`，那么按\_**key**，\_is**Key**，**key**，is**Key**的顺序直接搜索成员名
  5. 再没查到，调用`valueForUndefinedKey:`

  这有一篇KVC的文章，[前往](http://blog.csdn.net/u010123208/article/details/40425147)

- KVC实现原理：

  KVC运用了一个isa-swizzling技术。isa-swizzling就是类型混合指针机制。KVC主要通过isa-swizzling，来实现其内部查找定位的。isa指针，如其名称所指，（就是is a kind of的意思），指向维护分发表的对象的类。该分发表实际上包含了指向实现类中的方法的指针，和其它数据

- KVO实现原理：

  当某个类的对象第一次被观察时，系统就会在运行期动态地创建该类的一个派生类，在这个派生类中重写基类中任何被观察属性的 setter 方法。派生类在被重写的 setter 方法实现真正的通知机制。这么做是基于设置属性会调用 setter 方法，而通过重写就获得了 KVO 需要的通知机制。当然前提是要通过遵循 KVO 的属性设置方式来变更属性值，如果仅是直接修改属性对应的成员变量，是无法实现 KVO 的。同时派生类还重写了 class 方法以“欺骗”外部调用者它就是起初的那个类。然后系统将这个对象的 isa 指针指向这个新诞生的派生类，因此这个对象就成为该派生类的对象了，因而在该对象上对 setter 的调用就会调用重写的 setter，从而激活键值通知机制。此外，派生类还重写了 dealloc 方法来释放资源

  这有一篇KVO的文章，[前往](http://www.jianshu.com/p/e59bb8f59302)

### 将一个函数在主线程执行的4种方法

- GCD

  ```objective-c
  dispatch_async(dispatch_get_main_queue(), ^{
  	<#code#>
  });
  ```

- NSOperation

  ```objective-c
  NSOperationQueue *mainQueue = [NSOperationQueue mainQueue];
  NSBlockOperation *operation = [NSBlockOperation blockOperationWithBlock:^{
      <#code#>
  }];
  [mainQueue addOperation:operation];
  ```

- NSthread

  ```objective-c
  [[NSThread mainThread] performSelector:@selector(<#selector#>) withObject:nil];
  ```

- RunLoop

  ```objective-c
  [[NSRunLoop mainRunLoop] performSelector:@selector(<#selector#>) withObject:nil];
  ```

### 如何让计时器调用一个类方法

```objective-c
[NSTimer scheduledTimerWithTimeInterval:1.0 repeats:NO block:^(NSTimer * _Nonnull timer) {
	[[self class] classMethod];
}];

+ (void)classMethod {
    NSLog(@"classMethod");
}
```

### 如何重写类方法

在子类中实现一个和父类一样的类方法即可

### NSTimer创建后，会在哪个线程运行

- 使用`scheduledTimerWithTimeInterval:`创建的timer，在哪个线程创建就会加入哪个线程的RunLoop中运行
- 使用`timerWithTimeInterval:`和`initWithFireDate:`创建的timer，添加到哪个线程的RunLoop就会运行在哪个线程

### id和NSObject *的区别

- `id`是一个`objc_object`结构体指针，是一种通用的对象类型，可以指向所有的`OC`对象而不仅限于`NSObject`，并且编译器不会做类型检查

  ```objective-c
  typedef struct objc_object *id;
  struct objc_object {
      Class _Nonnull isa  OBJC_ISA_AVAILABILITY;
  };
  ```


- `NSObject *`是`NSObject`类型的指针，只能指向`NSObject`及其子类，调用的也只能是`NSObject`里面的方法否则就要做强制类型转换
- 在OC中，不是所有的类都是继承于`NSObject`，所以`id`的范围比`NSObject *`广

### 编写一个冒泡排序

```c
for (int i = 0; i < n - 1; i++) {
    for (int j = 0; j < n - 1 - i; j++) {
        if (arr[j] > arr[j + 1]) {
            temp = arr[j];
            arr[j] = arr[j + 1];
            arr[j + 1] = temp;
        }
    }
}
```

### 什么是ARC?（ARC是为了解决什么问题诞生的？）

自动引用计数 ( ARC ) 是指内存管理中对引用采取自动计数的技术。在 LLVM 编译器中设置 ARC 为有效状态，就无需再次键入 retain 或 release 代码，这在降低程序崩溃、内存泄漏等风险的同时，很大程度上减少了开发程序的工作量。

### 请解释以下 Keywords 的区别：assign vs weak, \_\_block vs \_\_weak

### \_\_block 在 ARC 和 非 ARC 下含义一样吗？

### 使用 atomic 一定是线程安全的吗？

### 描述一个你遇到过的 retain cycle 例子

### +(void)load; +(void)initialize; 有什么用处？

### 为什么其他语言里叫函数调用，Objective-C 里则是给对象发消息（或者谈下对runtime的理解）

### 什么是 method swizzling？

### UIView 和 CALayer 是啥关系？

### 如何高性能的给 UIImageView 加个圆角？（不准说 layer.cornerRadius ）

### 使用 drawRect 有什么影响？

### ASIHttpRequest 或者 SDWebImage 里面给UIImageView 加载图片的逻辑是什么样的？（把 UIImageView 放到 UITableViewCell 里面问更赞）

### 麻烦你设计个简单的图片内存缓存器（移除策略是一定要说的）

### 讲讲你用 Instruments 优化动画性能的经历

### loadView 是干嘛用的？

### viewWillLayoutSubView 你总是知道的

### GCD 里面有哪几种 Queue？

### 触摸事件的传递和响应

### 属性后面可以有哪些属性修饰符

### UIViewController的生命周期

### APP的生命周期

### #import 跟 #include ，@class 有什么区别？

### 适配不同尺寸屏幕的方案？

### 简单描述下客户端的缓存机制

### UIImage 初始化一张图片有几种方法？简述各自的优缺点

### 写一"标准"宏MIN，，这个宏输入两个参数并返回较小的一个

### 什么是序列化和反序列化，可以用来做什么？如何在 OC 中实现复杂对象的存储？

### 不用中间变量，用两种方法交换 A 和 B 的值

### 打印的结果

```c
main() {
  int a[5] = {1, 2, 3, 4, 5};
  int *ptr = (int *)(&a + 1);
  printf("%d, %d", *(a + 1), *(ptr - 1));
}
```

### 请输入执行结果，并解释为什么？

```objective-c
NSLog(@"222");
dispatch_sync(dispatch_get_main_queue(), ^() {
    NSLog(@"222");
});
NSLog(@"222");
```

### 写一个线程安全的单例类

### 假设有一个字符串 aabcad ，请写一段程序，去掉字符串中不相邻的重复字符串，即上述字符串处理之后的输出结果为：aabcd

### 用 obj-c 写一个快速排序

