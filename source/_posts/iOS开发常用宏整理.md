---
title: iOS开发常用宏整理
date: 2017-02-20 10:01:26
tags:
	- iOS
---

使用宏不仅方便，而且可以提高开发效率。整理了一下在自己项目中常用的宏。
以下均写在`Macro.h`文件中。

<!-- more -->

```object-c
// 一些缩写
#define kApplication            [UIApplication sharedApplication]
#define kKeyWindow              [UIApplication sharedApplication].keyWindow
#define kAppDelegate            [UIApplication sharedApplication].delegate
#define kUserDefaults           [NSUserDefaults standardUserDefaults]
#define kNotificationCenter     [NSNotificationCenter defaultCenter]
#define kDevice                 [UIDevice currentDevice]

// 获取屏幕宽度与高度
#define SCREEN_WIDTH            [UIScreen mainScreen].bounds.size.width
#define SCREEN_HEIGHT           [UIScreen mainScreen].bounds.size.height
#define SCREEN_SIZE             [UIScreen mainScreen].bounds.size
#define SCREEN_MAX_LENGTH       MAX(SCREEN_WIDTH, SCREEN_HEIGHT)
#define SCREEN_MIN_LENGTH       MIN(SCREEN_WIDTH, SCREEN_HEIGHT)

// 字符串是否为空
#define kStringIsEmpty(str)     (str == nil || [str isKindOfClass:[NSNull class]] || str.length == 0)
// 数组是否为空
#define kArrayIsEmpty(array)    (array == nil || [array isKindOfClass:[NSNull class]] || array.count == 0)
// 字典是否为空
#define kDictIsEmpty(dict)      (dict == nil || [dict isKindOfClass:[NSNull class]] || dict.allKeys.count == 0)
// 是否是空对象
#define kObjectIsEmpty(_object) (_object == nil || [_object isKindOfClass:[NSNull class]] || ([_object respondsToSelector:@selector(length)] && [(NSData *)_object length] == 0) || ([_object respondsToSelector:@selector(count)] && [(NSArray *)_object count] == 0))
// 判断是否为iPhone
#define IS_IPHONE               (UI_USER_INTERFACE_IDIOM() == UIUserInterfaceIdiomPhone)
// 判断是否为iPad
#define IS_IPAD                 (UI_USER_INTERFACE_IDIOM() == UIUserInterfaceIdiomPad)
// 判断型号尺寸.5为4寸机型，6为4.7寸机型，6P为5.5寸机型
#define IS_IPHONE_4_OR_LESS     (IS_IPHONE && SCREEN_MAX_LENGTH < 568.f)
#define IS_IPHONE_5             (IS_IPHONE && SCREEN_MAX_LENGTH == 568.f)
#define IS_IPHONE_6             (IS_IPHONE && SCREEN_MAX_LENGTH == 667.f)
#define IS_IPHONE_6P            (IS_IPHONE && SCREEN_MAX_LENGTH == 736.f)

// APP名称
#define kAppName                [[NSBundle mainBundle] infoDictionary] objectForKey:@"CFBundleDisplayName"]
// APP版本号
#define kAppVersion             [[NSBundle mainBundle] infoDictionary] objectForKey:@"CFBundleShortVersionString"]
// APP build号
#define kAppbuild               [[NSBundle mainBundle] infoDictionary] objectForKey:@"CFBundleVersion"]
// 系统版本号
#define kSystemVersion          [[UIDevice currentDevice] systemVersion]
// 获取当前语言
#define kCurrentLanguage        [[NSLocale preferredLanguages] objectAtIndex:0]
// 获取沙盒Documents路径
#define kDocumentsPath          [NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES) firstObject]
// 获取沙盒temp路径
#define kTempPath               NSTemporaryDirectory()
// 获取沙盒Cache路径
#define kCachePath              [NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) firstObject]

// 颜色
#define kRGBColor(r, g, b)      [UIColor colorWithRed:(r) / 255.f green:(g) / 255.f blue:(b) / 255.f alpha:1.f]
#define kRGBAColor(r, g, b, a)  [UIColor colorWithRed:(r) / 255.f green:(r) / 255.f blue:(r) / 255.f alpha:a]
#define kRandomColor            kRGBColor(arc4random_uniform(256), arc4random_uniform(256), arc4random_uniform(256))
#define kColorWithHex(rgbValue) [UIColor colorWithRed:((float)((rgbValue & 0xFF0000) >> 16)) / 255.f green:((float)((rgbValue & 0xFF00) >> 8)) / 255.f blue:((float)(rgbValue & 0xFF)) / 255.f alpha:1.f]

// 弱引用/强引用.在Code Snippet中添加@kWeakObj(<#obj#>)和@kStrongObj(<#obj#>);
#define kWeakObj(obj)           autoreleasepool{} __weak typeof(obj) weak##obj = obj;
#define kStrongObj(obj)         autoreleasepool{} __strong typeof(obj) obj = weak##obj;

// 由角度转换弧度
#define kDegreesToRadian(x)     (M_PI * x / 180.f)
// 由弧度转换角度
#define kRadianToDegrees(x)     (x * 180.f) / (M_PI)

// 获取一段时间间隔
#define kStartTime              CFAbsoluteTime start = CFAbsoluteTimeGetCurrent();
#define kEndTime                NSLog(@"Time: %f", CFAbsoluteTimeGetCurrent() - start)

// 开发的时候打印，但是发布的时候不打印的NSLog
#ifdef DEBUG
    #define NSLog(...)          NSLog(@"%s [Line %d]\n%@", __func__, __LINE__, [NSString stringWithFormat:__VA_ARGS__])
//    #define DLog(fmt, ...)      NSLog((@"%s [Line %d]\n" fmt), __PRETTY_FUNCTION__, __LINE__, ##__VA_ARGS__);
#else
    #define NSLog(...)
//    #define DLog(...)
#endif

// 判断是真机还是模拟器
#if TARGET_OS_IPHONE
// 真机
#endif

#if TARGET_IPHONE_SIMULATOR
// 模拟器
#endif
```