---
title: UITabbarController左右滑动切换标签页
date: 2017-04-12 09:25:03
tags:
	- iOS
---

为UINavigationController添加Category获取根视图控制器

```objective-c
- (UIViewController *)ww_rootViewController {
    if (self.viewControllers && [self.viewControllers count] > 0) {
        return [self.viewControllers firstObject];
    }
    return nil;
}
```

<!-- more -->

在基类BaseViewController中添加判断，只有根控制器才添加切换手势

```objective-c
- (void)viewDidLoad {
    [super viewDidLoad];
    if ([self.navigationController.ww_rootViewController isKindOfClass:[self class]]) {
        [self addSwitchGestureRecognizer];
    }
}
```

实现`addSwitchGestureRecognizer`

```objective-c
- (void)addSwitchGestureRecognizer {
    UISwipeGestureRecognizer *swipeLeft = [[UISwipeGestureRecognizer alloc] initWithTarget:self action:@selector(baseGestureRightButton:)];
    [swipeLeft setDirection:UISwipeGestureRecognizerDirectionLeft];
    [self.view addGestureRecognizer:swipeLeft];
    
    UISwipeGestureRecognizer *swipeRight = [[UISwipeGestureRecognizer alloc] initWithTarget:self action:@selector(baseGestureLeftButton:)];
    [swipeRight setDirection:UISwipeGestureRecognizerDirectionRight];
    [self.view addGestureRecognizer:swipeRight];
}
```

右滑事件，注释里面是另外的动画

```objective-c
- (void)baseGestureRightButton:(id)sender {
    NSUInteger selectedIndex = [self.tabBarController selectedIndex];
    NSArray *viewControllerArray = self.tabBarController.viewControllers;
    /*
    if (selectedIndex < viewControllerArray.count - 1) {
        [self.tabBarController setSelectedIndex:selectedIndex + 1];
        //To animate use this code
        CATransition *anim= [CATransition animation];
        [anim setType:kCATransitionPush];
        [anim setSubtype:kCATransitionFromRight];
        [anim setDuration:0.4f];
        [anim setTimingFunction:[CAMediaTimingFunction functionWithName:
                                 kCAMediaTimingFunctionEaseIn]];
        [self.tabBarController.view.layer addAnimation:anim forKey:@"fadeTransition"];
    }
    */
    /*
    if (selectedIndex < viewControllerArray.count - 1) {
        UIView *fromView = [self.tabBarController.selectedViewController view];
        UIView *toView = [[self.tabBarController.viewControllers objectAtIndex:selectedIndex + 1] view];
        [UIView transitionFromView:fromView toView:toView duration:0.5f options:UIViewAnimationOptionTransitionFlipFromRight completion:^(BOOL finished) {
            if (finished) {
                [self.tabBarController setSelectedIndex:selectedIndex + 1];
            }
        }];
    }
    */
    if (selectedIndex < viewControllerArray.count - 1) {
        NSUInteger controllerIndex = selectedIndex + 1;
        // Get the views.
        UIView *fromView = self.tabBarController.selectedViewController.view;
        UIView *toView = [[viewControllerArray objectAtIndex:controllerIndex] view];
        
        // Get the size of the view area.
        CGRect viewSize = fromView.frame;
        BOOL scrollRight = controllerIndex > self.tabBarController.selectedIndex;
        
        // Add the to view to the tab bar view.
        [fromView.superview addSubview:toView];
        
        // Position it off screen.
        toView.frame = CGRectMake((scrollRight ? viewSize.size.width : -viewSize.size.width), viewSize.origin.y, viewSize.size.width, viewSize.size.height);
        
        [UIView animateWithDuration:0.5f
                         animations: ^{
                             // Animate the views on and off the screen. This will appear to slide.
                             fromView.frame = CGRectMake((scrollRight ? -viewSize.size.width : viewSize.size.width), viewSize.origin.y, viewSize.size.width, viewSize.size.height);
                             toView.frame = CGRectMake(0, viewSize.origin.y, viewSize.size.width, viewSize.size.height);
                         }
                         completion:^(BOOL finished) {
                             if (finished) {
                                 // Remove the old view from the tabbar view.
                                 [fromView removeFromSuperview];
                                 self.tabBarController.selectedIndex = controllerIndex;
                             }
                         }];
    }
}
```

左滑事件，注释里面是另外的动画

```objective-c
- (void)baseGestureLeftButton:(id)sender {
    NSUInteger selectedIndex = [self.tabBarController selectedIndex];
    NSArray *viewControllerArray = self.tabBarController.viewControllers;
    /*
    if (selectedIndex > 0) {
        [self.tabBarController setSelectedIndex:selectedIndex - 1];
        CATransition *anim= [CATransition animation];
        [anim setType:kCATransitionPush];
        [anim setSubtype:kCATransitionFromLeft];
        [anim setDuration:0.4f];
        [anim setTimingFunction:[CAMediaTimingFunction functionWithName:
                                 kCAMediaTimingFunctionEaseIn]];
        [self.tabBarController.view.layer addAnimation:anim forKey:@"fadeTransition"];
    }
    */
    /*
    if (selectedIndex > 0) {
        UIView *fromView = [self.tabBarController.selectedViewController view];
        UIView *toView = [[self.tabBarController.viewControllers objectAtIndex:selectedIndex - 1] view];
        [UIView transitionFromView:fromView toView:toView duration:0.5f options:UIViewAnimationOptionTransitionFlipFromLeft completion:^(BOOL finished) {
            if (finished) {
                [self.tabBarController setSelectedIndex:selectedIndex - 1];
            }
        }];
    }
    */
    if (selectedIndex > 0) {
        NSUInteger controllerIndex = selectedIndex - 1;
        // Get the views.
        UIView *fromView = self.tabBarController.selectedViewController.view;
        UIView *toView = [[viewControllerArray objectAtIndex:controllerIndex] view];
        
        // Get the size of the view area.
        CGRect viewSize = fromView.frame;
        BOOL scrollRight = controllerIndex > self.tabBarController.selectedIndex;
        
        // Add the to view to the tab bar view.
        [fromView.superview addSubview:toView];
        
        // Position it off screen.
        toView.frame = CGRectMake((scrollRight ? viewSize.size.width : -viewSize.size.width), viewSize.origin.y, viewSize.size.width, viewSize.size.height);
        
        [UIView animateWithDuration:0.5f
                         animations: ^{
                             // Animate the views on and off the screen. This will appear to slide.
                             fromView.frame = CGRectMake((scrollRight ? -viewSize.size.width : viewSize.size.width), viewSize.origin.y, viewSize.size.width, viewSize.size.height);
                             toView.frame = CGRectMake(0, viewSize.origin.y, viewSize.size.width, viewSize.size.height);
                         }
                         completion:^(BOOL finished) {
                             if (finished) {
                                 // Remove the old view from the tabbar view.
                                 [fromView removeFromSuperview];
                                 self.tabBarController.selectedIndex = controllerIndex;
                             }
                         }];
    }
}
```

# 参考资料

[ios-swipe-left-right-between-tabs-possible](http://stackoverflow.com/questions/12533976/ios-swipe-left-right-between-tabs-possible)

[iphone-how-to-switch-tabs-with-an-animation](http://stackoverflow.com/questions/5161730/iphone-how-to-switch-tabs-with-an-animation)