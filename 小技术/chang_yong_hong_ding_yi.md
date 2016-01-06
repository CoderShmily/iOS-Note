# 常用宏定义

- ##### 根据版本是debug还是release禁用NSLog

```objc
/** 日志输出 */
#ifdef DEBUG // 开发
#define XMGLog(...) NSLog(__VA_ARGS__)
#else // 发布
#define XMGLog(...)
#endif
```


- ##### 判断iOS版本和设备

```objc
// iOS版本号
#define IOS7 ([[[UIDevice currentDevice].systemVersion doubleValue] >= 7.0])

// 设备型号
判断当前设备是不是iPhone5
#define kScreenIphone5 (([[UIScreen mainScreen] bounds].size.height)>=568)
```
- ##### 硬件信息的获取