0 - 笔记
```objc
1. [super dealloc]; // 要调用super的此方法,要在MRC下

2. @property (nonatomic, copy, getter=isRich) BOOL rich; // 指定getter方法

3. 枚举的写法 类型:CYLSex
  typedef NS_ENUM(NSInteger, CYLSex) {
      CYLSexMan,
      CYLSexWoman
  };
4. #import "1.h"
编译阶段拷贝"1.h"内容
如果"1.h"改变,所有包含的都要重新编译
头文件@class引入类,.m文件再#import "1.h"
```


