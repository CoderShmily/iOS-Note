### 线程状态
- 新建(New) 需要start才能进入就绪
- 就绪(Runnable)
- 运行(Running)
- 阻塞(Blocked)
- 死亡(Dead)


### 创建线程的方法

```objc
1. [NSThread detachNewThreadSelector:SEL toTarget:id withObject:id];

2. [self performSelectorInBackground:SEL withObject:id];

3. NSThread *thread = [[NSThread alloc] initWithTarget:self selector:SEL object:nil];

# 1.2.创建后自动启动  3需要自己start
```

### 主线程相关用法

```objc
+ (NSThread *)mainThread; // 获得主线程
- (BOOL)isMainThread; // 是否为主线程
+ (BOOL)isMainThread; // 是否为主线程

```
### 获得当前线程
```objc
NSThread *current = [NSThread currentThread];
```

