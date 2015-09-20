pch常用条件编译

```objc
#ifdef __OBJC__
  
#endif

#ifdef DEBUG // 开发
#define XMGLog(...) NSLog(__VA_ARGS__)
#else // 发布
#define XMGLog(...)
#endif

```