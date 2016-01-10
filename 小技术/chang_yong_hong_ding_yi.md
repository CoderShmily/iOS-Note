# 常用宏定义

- ##### 1
```objc
#warning 警告提示
#pragma marks 分割
// TODO: 等待实现的功能
/* TODO: 等待实现的功能 */
// FIXME: 需要修正的功
/* FIXME: 需要修正的功 */
// !!!: 需要改进的功能
/* !!!: 需要改进的功能 */
```

- ##### 1.根据版本是debug还是release禁用NSLog

```objc
/** 日志输出 */
#ifdef DEBUG // 开发
#define MYLog(...) NSLog(__VA_ARGS__)
#else // 发布
#define MYLog(...)
#endif

//重写NSLog,Debug模式下打印日志和当前行数
#if DEBUG
#define NSLog(FORMAT, ...) fprintf(stderr,"\nfunction:%s line:%d content:%s\n", __FUNCTION__, __LINE__, [[NSString stringWithFormat:FORMAT, ##__VA_ARGS__] UTF8String]);
#else
#define NSLog(FORMAT, ...) nil
#endif
```
- ##### 2.判断是否是OC语言
```objc
#ifdef __OBJC__
  #import <UIKit/UIKit.h>
  #import <Foundation/Foundation.h>
#endif
```
- ##### 3.判断是ARC还是MRC
```objc
#if __has_feature(objc_arc)
// 如果是ARC什么都不添加
#else
#endif
```

- ##### 4.判断iOS版本和设备

```objc
// 是否大于iOS7
#define IOS7 ([[[UIDevice currentDevice].systemVersion doubleValue] >= 7.0])

// 如果定义此宏，说明iOS SDK大于8.0
#ifndef __IPHONE_8_0
#warning "This project uses features only available in iOS SDK 4.0 and later."
#endif

// 当前系统支持的最小版本
__IPHONE_OS_VERSION_MIN_REQUIRED;
// 当前系统支持的最大版本
__IPHONE_OS_VERSION_MAX_ALLOWED;
#if __IPHONE_OS_VERSION_MAX_ALLOWED > __IPHONE_2_2
    // iPhone OS SDK 3.0 以后版本的处理
#else
    // iPhone OS SDK 3.0 之前版本的处理
#endif
```

```objc
#if TARGET_IPHONE_SIMULATOR
    NSLog(@"模拟器");
#else
    NSLog(@"真机");
#endif

#if !TARGET_IPHONE_SIMULATOR
    NSLog(@"真机");
#endif

// iPhone iPad
if ([[[UIDevice currentDevice] model] isEqualToString:@"iPhone"]) {
    // iPhone 不分真机、模拟器
    NSLog(@"%@", [[UIDevice currentDevice] model]);
}
if ([[[UIDevice currentDevice] model] isEqualToString:@"iPad"]) {
    // iPad
    NSLog(@"%@", [[UIDevice currentDevice] model]);
}
```

```objc
// 1.设备型号iPhone5
#define kScreenIphone5 (([[UIScreen mainScreen] bounds].size.height) >= 568)
```
```objc
// 2.真机有型号 模拟器显示一样
#import <sys/sysctl.h>
+ (NSString*)getDeviceVersion
{
    size_t size;
    sysctlbyname("hw.machine",NULL, &size, NULL,0);
    char *machine = (char*)malloc(size);
    sysctlbyname("hw.machine", machine, &size,NULL, 0);
    NSString *platform = [NSString stringWithCString:machine encoding:NSUTF8StringEncoding];
    //NSString *platform = [NSStringstringWithUTF8String:machine];二者等效
    free(machine);
    return platform;
}

+ (NSString *)getPlatformString
{
    NSString *platform = [self getDeviceVersion];
    
    //iPhone
    if ([platform isEqualToString:@"iPhone1,1"]) return @"iPhone 1";
    if ([platform isEqualToString:@"iPhone1,2"]) return @"iPhone 3";
    if ([platform isEqualToString:@"iPhone2,1"]) return @"iPhone 3GS";
    if ([platform isEqualToString:@"iPhone3,1"]) return @"iPhone 4";
    if ([platform isEqualToString:@"iPhone3,2"]) return @"iPhone 4";
    if ([platform isEqualToString:@"iPhone3,3"]) return @"iPhone 4";
    if ([platform isEqualToString:@"iPhone4,1"]) return @"iPhone 4s";
    if ([platform isEqualToString:@"iPhone5,1"]) return @"iPhone 5";
    if ([platform isEqualToString:@"iPhone5,2"]) return @"iPhone 5";
    if ([platform isEqualToString:@"iPhone5,3"]) return @"iPhone 5C";
    if ([platform isEqualToString:@"iPhone5,4"]) return @"iPhone 5C";
    if ([platform isEqualToString:@"iPhone6,1"]) return @"iPhone 5S";
    if ([platform isEqualToString:@"iPhone6,2"]) return @"iPhone 5S";
    if ([platform isEqualToString:@"iPhone7,1"]) return @"iPhone 6";
    if ([platform isEqualToString:@"iPhone7,2"]) return @"iPhone 6 Plus";
    
    //iPot Touch
    if ([platform isEqualToString:@"iPod1,1"]) return @"iPod Touch";
    if ([platform isEqualToString:@"iPod2,1"]) return @"iPod Touch 2";
    if ([platform isEqualToString:@"iPod3,1"]) return @"iPod Touch 3";
    if ([platform isEqualToString:@"iPod4,1"]) return @"iPod Touch 4";
    if ([platform isEqualToString:@"iPod5,1"]) return @"iPod Touch 5";
    
    //iPad
    if ([platform isEqualToString:@"iPad1,1"]) return @"iPad";
    if ([platform isEqualToString:@"iPad2,1"]) return @"iPad 2";
    if ([platform isEqualToString:@"iPad2,2"]) return @"iPad 2";
    if ([platform isEqualToString:@"iPad2,3"]) return @"iPad 2";
    if ([platform isEqualToString:@"iPad2,4"]) return @"iPad 2";
    if ([platform isEqualToString:@"iPad2,5"]) return @"iPad Mini 1";
    if ([platform isEqualToString:@"iPad2,6"]) return @"iPad Mini 1";
    if ([platform isEqualToString:@"iPad2,7"]) return @"iPad Mini 1";
    if ([platform isEqualToString:@"iPad3,1"]) return @"iPad 3";
    if ([platform isEqualToString:@"iPad3,2"]) return @"iPad 3";
    if ([platform isEqualToString:@"iPad3,3"]) return @"iPad 3";
    if ([platform isEqualToString:@"iPad3,4"]) return @"iPad 4";
    if ([platform isEqualToString:@"iPad3,5"]) return @"iPad 4";
    if ([platform isEqualToString:@"iPad3,6"]) return @"iPad 4";
    if ([platform isEqualToString:@"iPad4,1"]) return @"iPad air";
    if ([platform isEqualToString:@"iPad4,2"]) return @"iPad air";
    if ([platform isEqualToString:@"iPad4,3"]) return @"iPad air";
    if ([platform isEqualToString:@"iPad4,4"]) return @"iPad mini 2";
    if ([platform isEqualToString:@"iPad4,5"]) return @"iPad mini 2";
    if ([platform isEqualToString:@"iPad4,6"]) return @"iPad mini 2";
    if ([platform isEqualToString:@"iPad4,7"]) return @"iPad mini 3";
    if ([platform isEqualToString:@"iPad4,8"]) return @"iPad mini 3";
    if ([platform isEqualToString:@"iPad4,9"]) return @"iPad mini 3";
    if ([platform isEqualToString:@"iPad5,3"]) return @"iPad air 2";
    if ([platform isEqualToString:@"iPad5,4"]) return @"iPad air 2";
    
    if ([platform isEqualToString:@"iPhone Simulator"] || [platform isEqualToString:@"x86_64"]) return @"iPhone Simulator";
    return platform;
    
}
```