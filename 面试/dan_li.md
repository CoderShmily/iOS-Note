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
        _instance = [super allocWithZone:zone];
    }
     */
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        _instance = [super allocWithZone:zone];
    });
    return _instance;
}
- (id)copyWithZone:(NSZone *)zone
{
    // 因为copy方法必须通过实例对象调用, 所以可以直接返回_instance
//    return [[self class] allocWithZone:zone];
    return _instance;
}

- (id)mutableCopyWithZone:(NSZone *)zone
{
//    return [[self class] allocWithZone:zone];
    return _instance;
}

```