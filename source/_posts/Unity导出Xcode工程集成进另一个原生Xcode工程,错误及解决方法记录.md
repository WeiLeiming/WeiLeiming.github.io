---
title: 'Unity导出Xcode工程集成进另一个原生Xcode工程,错误及解决方法记录'
date: 2017-03-02 11:23:02
tags:
	- Unity
	- iOS
typora-copy-images-to: ipic
---

最近在研究Unity导出的Xcode工程如何集成在现有工程中。现将过程中遇见的问题整理如下。

<!-- more -->

# Unity导出Xcode工程编译错误

1. MapFileParser.sh: Permission denied

   ![ADB6928C-7EF7-4469-8CEC-856B504A63B3](https://ww3.sinaimg.cn/large/006tNc79ly1fd8c4lh1l8j30mv01dt96.jpg)

   打开终端，进入`MapFileParser.sh`所在目录，执行

   ```
   $ chmod 777 MapFileParser.sh
   ```

2. 推送警告

   ![E432AF53-86D7-44A7-9649-F262E3EBC351](http://ww4.sinaimg.cn/large/006tKfTcly1ff1c19i81gj30n2014t93.jpg)

   解决方法Capablilties->Background Modes->Remote notifications

   ![75BDC5C5-7DC9-4CB4-A55C-1959E7198C10](http://ww4.sinaimg.cn/large/006tKfTcly1ff1bzgczroj30o40afta7.jpg)


# Unity导出Xcode工程集成进原生Xcode工程

## 集成步骤

### Unity导出Xcode工程
在iOS的**Player Setting**中进行设置

- Resolution and Presentation

  根据项目需要设定，需要注意的是Orientation方向必须是原生工程Orientation方向的子集

- Other Settings
  - 消掉Auto Graphics API，Graphics APIs列表中只留OpenGLES2
  - Scripting Backend选择IL2CPP
  - Bundle Identifier、Target Device、Target minimum iOS Version根据原生工程设置

这是我导出来的工程目录，设备只选择了iPhone

![111](https://ww4.sinaimg.cn/large/006tNc79ly1fdgmwn9tqhj305c06ljrq.jpg)

### 集成工程

> **注意：**Build Setting和Build Phases中的设置均是根据Unity_Xcode中设置得到的，集成的时候对照两个工程的设置集成

- 我在原生工程目录下新建了一个Unity文件夹来放置从Unity_Xcode工程中拷贝过来需要使用的文件

![222](https://ww2.sinaimg.cn/large/006tNc79ly1fdgmyx5urlj30gu06pjs1.jpg)

- 将文件添加进工程目录

将Classes、Libraries、MapFileParser.sh添加进工程目录

![333](https://ww2.sinaimg.cn/large/006tNc79ly1fdgogg7fsij30l80cw752.jpg)

将Data添加进工程目录

![444](https://ww4.sinaimg.cn/large/006tNc79ly1fdgoft719rj30l80cwaau.jpg)

注意这两种不同的添加形式

- 添加framework

根据自己导出的Unity_Xcode工程添加

![555](https://ww3.sinaimg.cn/large/006tNc79ly1fdgoxk1oncj30nj0daabs.jpg)

- Build Settings

设置Header Search Paths

![111](https://ww3.sinaimg.cn/large/006tNc79ly1fdgp5v2yb8j30jj0660th.jpg)

设置Library Search Paths

![444](https://ww1.sinaimg.cn/large/006tNc79ly1fdgphvrzdij30jj067mxq.jpg)

设置Other Linker Flags

![333](https://ww3.sinaimg.cn/large/006tNc79ly1fdgplwi962j30j30670t2.jpg)

设置Other C Flags & Other C++ Flags

![222](https://ww4.sinaimg.cn/large/006tNc79ly1fdgpqzi4a9j30jj067dg5.jpg)

设置C Language Dialect

![222](https://ww4.sinaimg.cn/large/006tNc79ly1fdgpsr63j4j30eq00ldfq.jpg)

设置C++ Language Dialect

![222](https://ww4.sinaimg.cn/large/006tNc79ly1fdgptuhwqkj30e700ngli.jpg)

设置C++ Standard Library

![222](https://ww1.sinaimg.cn/large/006tNc79ly1fdgpuvg8h4j30j000pmx5.jpg)

设置Enable Bitcode

![222](https://ww4.sinaimg.cn/large/006tNc79ly1fdgpvqt66yj30c200m0sl.jpg)

Add User-Defined Setting

![222](https://ww4.sinaimg.cn/large/006tNc79ly1fdgpypkbzrj30fy01vglq.jpg)

添加GCC_THUMB_SUPPORT

添加GCC_USE_INDIRECT_FUNCTION_CALLS

添加UNITY_RUNTIME_VERSION。此项根据Unity导出的版本填写。

添加UNITY_SCRIPTING_BACKEND

![222](https://ww3.sinaimg.cn/large/006tNc79ly1fdgq23tobej30fs03m74n.jpg)

- Build Phases

添加Run Script

![222](https://ww2.sinaimg.cn/large/006tNc79ly1fdgq4o44sjj30mg05k0ta.jpg)

添加"$PROJECT_DIR/iOS_Unity_Test01/Unity/MapFileParser.sh"

![222](https://ww4.sinaimg.cn/large/006tNc79ly1fdgq7dgx89j30st098js0.jpg)

- 新建PCH文件，命名为PrefixHeader。将Classes中的Prefix.pch文件的内容全部拷贝到Supporting Files中的PrefixHeader.pch中，并删除Classes中的Prefix.pch文件引用
- 将Classes/main.mm全部内容复制到main.m并把扩展名改为.mm，然后删除Classes/main.mm文件引用，注意红色框圈住的修改细节

![222](https://ww1.sinaimg.cn/large/006tNc79ly1fdgql76hiuj30ou0fkn0n.jpg)

- 修改UnityAppController.h

 ```objective-c
#import "AppDelegate.h"
inline UnityAppController*	GetAppController()
{
    return (UnityAppController *)[[UIApplication sharedApplication] valueForKeyPath:@"delegate.unityController"];
}
 ```

- 修改AppDelegate.h

```objective-c
#import <UIKit/UIKit.h>
#import "UnityAppController.h"

@interface AppDelegate : UIResponder <UIApplicationDelegate>

@property (strong, nonatomic) UIWindow *window;
@property (strong, nonatomic) UIWindow *unityWindow;
@property (strong, nonatomic) UnityAppController *unityController;

- (void)showUnityWindow;
- (void)hideUnityWindow;

@end
```

- 修改AppDelegate.m

```objective-c
@implementation AppDelegate

- (UIWindow *)unityWindow {
    return UnityGetMainWindow();
}

- (void)showUnityWindow {
    [self.unityWindow makeKeyAndVisible];
  	UnityPause(false);
}

- (void)hideUnityWindow {
  	UnityPause(true);
    [self.window makeKeyAndVisible];
}

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.
    
    _unityController = [[UnityAppController alloc] init];
    [_unityController application:application didFinishLaunchingWithOptions:launchOptions];
    
    self.window = [[UIWindow alloc] initWithFrame:[[UIScreen mainScreen] bounds]];
    TestViewController *vc = [[TestViewController alloc] init];
    self.window.rootViewController = vc;
    [self.window makeKeyAndVisible];
    
    return YES;
}


- (void)applicationWillResignActive:(UIApplication *)application {
    // Sent when the application is about to move from active to inactive state. This can occur for certain types of temporary interruptions (such as an incoming phone call or SMS message) or when the user quits the application and it begins the transition to the background state.
    // Use this method to pause ongoing tasks, disable timers, and invalidate graphics rendering callbacks. Games should use this method to pause the game.
    [_unityController applicationWillResignActive:application];
}


- (void)applicationDidEnterBackground:(UIApplication *)application {
    // Use this method to release shared resources, save user data, invalidate timers, and store enough application state information to restore your application to its current state in case it is terminated later.
    // If your application supports background execution, this method is called instead of applicationWillTerminate: when the user quits.
    [_unityController applicationDidEnterBackground:application];
}


- (void)applicationWillEnterForeground:(UIApplication *)application {
    // Called as part of the transition from the background to the active state; here you can undo many of the changes made on entering the background.
    [_unityController applicationWillEnterForeground:application];
}


- (void)applicationDidBecomeActive:(UIApplication *)application {
    // Restart any tasks that were paused (or not yet started) while the application was inactive. If the application was previously in the background, optionally refresh the user interface.
    [_unityController applicationDidBecomeActive:application];
}


- (void)applicationWillTerminate:(UIApplication *)application {
    // Called when the application is about to terminate. Save data if appropriate. See also applicationDidEnterBackground:.
    [_unityController applicationWillTerminate:application];
}


@end
```

## 集成过程中遇见的一些问题

1. 编译完成出现许多`Code will never be executed`的警告⚠️

![111](https://ww2.sinaimg.cn/large/006tNc79ly1fdd7hrn1pjj304x010mx1.jpg)

解决方法：在`Build Settings`中将`Unreachable Code`设置为**No**可以消除警告

![222](https://ww3.sinaimg.cn/large/006tNbRwly1fdd7jiejdqj30c700n3yd.jpg)

# 参考资料

[iOS Player Settings](https://docs.unity3d.com/Manual/class-PlayerSettingsiOS.html)

[Unity3D工程并入iOS工程总结](http://www.jianshu.com/p/49fffc543140)

[unity3D与iOS原生工程项目合并以及合并过程中的问题](http://www.jianshu.com/p/f98bcfe09dc7)

[iOS - 将Unity导出的Xcode工程导入到另一个Xcode项目, 及常见报错的解决方法](http://blog.csdn.net/dylan_lwb_/article/details/51452470)

[将U3d工程嵌入iOS工程中](http://www.jianshu.com/p/487015f3bb13)