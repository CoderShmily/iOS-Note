# NSThread

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

# 1.2.创建后自动启动  3需要自己start线程
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

### 控制线程状态
```objc
1. 启动线程
- (void)start; 
// 进入就绪状态 -> 运行状态。当线程任务执行完毕，自动进入死亡状态

2. 阻塞（暂停）线程
+ (void)sleepUntilDate:(NSDate *)date;
+ (void)sleepForTimeInterval:(NSTimeInterval)ti;
// 进入阻塞状态

3. 强制停止线程
+ (void)exit;
// 进入死亡状态

# 注意：一旦线程停止（死亡）了，就不能再次开启任务
```
### 安全隐患解决 – 互斥锁

```objc
# 互斥锁使用格式
@synchronized(锁对象) { // 需要锁定的代码  }
# 注意：锁定1份代码只用1把锁，用多把锁是无效的
```
#### 互斥锁的优缺点
- 优点：能有效防止因多线程抢夺资源造成的数据安全问题
- 缺点：需要消耗大量的CPU资源

#### nonatomic和atomic对比
- atomic：原子属性，为setter方法加锁（默认就是atomic）,线程安全，需要消耗大量的资源
- nonatomic：非原子属性，不会为setter方法加锁
,非线程安全，适合内存小的移动设备

#### 互斥锁和自旋锁不同点
- 如果是互斥锁, 假如现在被锁住了, 那么后面来得线程就会进入”休眠”状态, 直到解锁之后, 又会唤醒线程继续执行
- 如果是自旋锁, 假如现在被锁住了, 那么后面来得线程不会进入休眠状态, 会一直傻傻的等待, 直到解锁之后立刻执行
自旋锁更适合做一些较短的操作

#### iOS开发的建议
- 所有属性都声明为nonatomic
- 尽量避免多线程抢夺同一块资源
- 尽量将加锁、资源抢夺的业务逻辑交给服务器端处理，减小移动客户端的压力

#### 线程间通信
```objc
# 线程间通信常用方法
- (void)performSelectorOnMainThread:(SEL)aSelector withObject:(id)arg waitUntilDone:(BOOL)wait;
- (void)performSelector:(SEL)aSelector onThread:(NSThread *)thr withObject:(id)arg waitUntilDone:(BOOL)wait;
```

#### 线程间通信方式 – 利用NSPort

#GCD
- 全称是Grand Central Dispatch，可译为“牛逼的中枢调度器”
- 纯C语言，提供了非常多强大的函数

#### GCD的优势
- GCD是苹果公司为多核的并行运算提出的解决方案
- GCD会自动管理线程的生命周期（创建线程、调度任务、销毁线程）

#### GCD中有2个核心概念
- 任务：执行什么操作
- 队列：用来存放任务

#### GCD的使用就2个步骤
- 定制任务
- 将任务添加到队列中
    - GCD会自动将队列中的任务取出，放到对应的线程中执行
    - 任务的取出遵循队列的FIFO原则：先进先出，后进后出
    
#### GCD执行任务

- 用同步的方式执行任务
- <font color = red>同步：只能在当前线程中执行任务，不具备开启新线程的能力</font>
```objc
dispatch_sync(dispatch_queue_t queue, dispatch_block_t block);
// queue：队列
// block：任务
```

- 用异步的方式执行任务
- <font color = red>异步：可以在新的线程中执行任务，具备开启新线程的能力</font>
```objc
dispatch_async(dispatch_queue_t queue, dispatch_block_t block);
```







