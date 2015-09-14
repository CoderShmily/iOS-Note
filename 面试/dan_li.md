```objc
@interface Tools : NSObject<NSCopying, NSMutableCopying>
// 一般情况下, 如果一个类是单例, 那么都会提供一个类方法用于快速创建单例对象
// 而且这个类方法的名称是有一定的规则: share + 类名称 / default + 类名称 / 类名称开头
+ (instancetype)shareTools;
@end
```


```objc
# 由于alloc方法内部会调用allocWithZone: 所以我们只需要保证在该方法只创建一个对象即可
```

```objc

@implementation Tools

+ (instancetype)shareTools
{
    return [[self alloc] init];
}

static Tools *_instance; // 程序运行期间只分配一分内存
+ (instancetype)allocWithZone:(struct _NSZone *)zone
{
    // 由于alloc方法内部会调用allocWithZone: 所以我们只需要保证在该方法只创建一个对象即可
    /* 
    if (_instance == nil) {
     // 如果多线程执行到此,有线程sleep,醒了可能已经分配过内存
     // 此方法不可靠
        _instance = [super allocWithZone:zone];
    }
     */
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        _instance = [super allocWithZone:zone];
    });
    return _instance;
}

// 让copy方法也返回同一个对象,copy会调用copyWithZone
- (id)copyWithZone:(NSZone *)zone
{
    // 因为copy方法必须通过实例对象调用,因为对象已经创建, 所以可以直接返回_instance
//    return [[self class] allocWithZone:zone];
    return _instance;
}

- (id)mutableCopyWithZone:(NSZone *)zone
{
//    return [[self class] allocWithZone:zone];
    return _instance;
}

```