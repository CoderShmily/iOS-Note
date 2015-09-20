- pch文件使用前要包含路径
    - 从工程目录下开始所以可省略为 00000/PrefixHeader.pch
    - 还有个$(SRC)什么的...

![](pch设置.png)


- pch常用条件编译

```objc
/** 判断是否OC语言 */
#ifdef __OBJC__
#endif

/** 判断是ARC还是MRC */
#if __has_feature(objc_arc)
// 如果是ARC什么都不添加
#else
#endif
```

```objc
/** 日志输出 */
#ifdef DEBUG // 开发
#define XMGLog(...) NSLog(__VA_ARGS__)
#else // 发布
#define XMGLog(...)
#endif
```