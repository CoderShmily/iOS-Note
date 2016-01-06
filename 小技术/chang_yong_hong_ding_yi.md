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