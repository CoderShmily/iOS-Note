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


- ##### 判断设备的操作系统版本和iOS版本

```objc
// 判断设备的操作系统版本
#define IOS7   ([[[UIDevice currentDevice].systemVersion doubleValue] >= 7.0])

判断当前设备是不是iPhone5
1.#define kScreenIphone5    (([[UIScreen mainScreen] bounds].size.height)>=568)
```
- ##### 硬件信息的获取