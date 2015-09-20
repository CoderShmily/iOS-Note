- 根据版本是debug还是release禁用NSLog
```
/** 日志输出 */
#ifdef DEBUG // 开发
#define XMGLog(...) NSLog(__VA_ARGS__)
#else // 发布
#define XMGLog(...)
#endif
```

- 格式化输出
```
//常用打印语句
  NSLog(@"字符串:%@",name);
  NSLog(@"字符:%c",a);
  NSLog(@"布尔值:%i",isShow);
  NSLog(@"整形:%i",i);
  NSLog(@"单精度浮点数： %f",f);
  NSLog(@"精度浮点数,且只保留两位小数:%.2f",f);
  NSLog(@"科学技术法:%e",f);
  NSLog(@"科学技术法(用最简短的方式):%g",f);
  NSLog(@"同时打印两个整数：i＝%i,f=%f",i,f);
```