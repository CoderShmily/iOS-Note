# 单例模式

单例模式的作用
```
1. 可以保证在程序运行过程，一个类只有一个实例，而且该实例易于供外界访问
2. 从而方便地控制了实例个数，并节约系统资源
```
- ARC中，单例模式的实现

```objc
// 提供1个类方法让外界访问唯一的实例
+ (instancetype)sharedTools
{
    return [[self alloc] init];
}

// 在.m中保留一个全局的static的实例
static Tools *_instance;

// 重写allocWithZone:方法，在这里创建唯一的实例
// 注意线程安全,判断是否为nil,可能多次创建
+ (instancetype)allocWithZone:(struct _NSZone *)zone
{
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        _instance = [super allocWithZone:zone];
    });
    return _instance;
}

// 实现copyWithZone:方法
- (id)copyWithZone:(struct _NSZone *)zone
{
    // 因为copy方法必须通过实例对象调用,所以可以直接返回_instance
    return _instance;
}

- (id)mutableCopyWithZone:(NSZone *)zone
{
    return _instance;
}

```

- MRC中,单例模式的实现

<font color=red>除了添加上面ARC实现的代码,再添加下面代码</font>

```objc
#pragma mark - MRC
#if __has_feature(objc_arc)
// 如果是ARC什么都不添加
#else
- (oneway void)release
{
    
}

- (instancetype)retain
{
    return _instance;
}

- (NSUInteger)retainCount
{
    return MAXFLOAT; //装逼格
}
#endif

```

<font color=red>单例不能继承,类的静态变量只被初始化一次,子类和父类谁先调用,创建的就是哪种类型的变量,以后也不会改变</font>


#### 创建很多单例

- 利用宏定义

```objc
//
//  Single.h
//  01-掌握-单例ARC
//
//  Created by apple on 15/8/6.
//  Copyright (c) 2015年 小码哥. All rights reserved.
//

#define interfaceSingle(name)  + (instancetype)share##name

#if __has_feature(objc_arc)
// 如果是ARC
#define implementationSingle(name)  + (instancetype)share##name \
{ \
    return [[self alloc] init]; \
} \
static id _instance; \
+ (instancetype)allocWithZone:(struct _NSZone *)zone \
{ \
    static dispatch_once_t onceToken; \
    dispatch_once(&onceToken, ^{ \
        _instance = [super allocWithZone:zone]; \
    }); \
    return _instance; \
} \
- (id)copyWithZone:(NSZone *)zone \
{ \
    return _instance; \
} \
- (id)mutableCopyWithZone:(NSZone *)zone \
{ \
    return _instance; \
}
#else
// 如果不是ARC
#define implementationSingle(name)  + (instancetype)share##name \
{ \
return [[self alloc] init]; \
} \
static id _instance; \
+ (instancetype)allocWithZone:(struct _NSZone *)zone \
{ \
static dispatch_once_t onceToken; \
dispatch_once(&onceToken, ^{ \
_instance = [super allocWithZone:zone]; \
}); \
return _instance; \
} \
- (id)copyWithZone:(NSZone *)zone \
{ \
return _instance; \
} \
- (id)mutableCopyWithZone:(NSZone *)zone \
{ \
return _instance; \
}\
- (oneway void)release \
{} \
- (instancetype)retain \
{ \
    return _instance; \
} \
- (NSUInteger)retainCount \
{ \
    return MAXFLOAT; \
}
#endif
```

- 使用宏定义

```objc
//  FileTools.h

#import <Foundation/Foundation.h>
#import "Single.h"

@interface FileTools : NSObject
//+ (instancetype)shareFileTools;
interfaceSingle(FileTools);
@end

```

```objc
//  FileTools.m

#import "FileTools.h"

@implementation FileTools

implementationSingle(FileTools)

@end

```
