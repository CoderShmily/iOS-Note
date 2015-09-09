## NSTimer的使用
- 开启定时器

```objc
@property (nonatomic, weak) NSTimer *timer;

// 返回一个自动开始执行任务的定时器
self.timer = [NSTimer scheduledTimerWithTimeInterval:2.0 target:self selector:@selector(nextPage:) userInfo:@"123" repeats:YES];

// 修改NSTimer在NSRunLoop中的模式：NSRunLoopCommonModes
// 主线程不管在处理什么操作，都会抽时间处理NSTimer
[[NSRunLoop mainRunLoop] addTimer:self.timer forMode:NSRunLoopCommonModes];
```
- 关闭定时器

```objc
[self.timer invalidate];
```
