### 线程状态
- 新建 需要start才能进入就绪
- 就绪
- 运行
- 阻塞
- 死亡


### 创建线程的方法

```objc
1. [NSThread detachNewThreadSelector:SEL toTarget:id withObject:id];

2. [self performSelectorInBackground:SEL withObject:id];

3. NSThread *thread = [[NSThread alloc] initWithTarget:self selector:SEL object:nil];
```


