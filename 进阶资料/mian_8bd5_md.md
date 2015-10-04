0 - 笔记
```objc
1. [super dealloc]; // 要调用super的此方法,要在MRC下
```
```objc
2. @property (nonatomic, copy, getter=isRich) BOOL rich; // 指定getter方法
```
```objc
3. 枚举的写法 类型:CYLSex
  typedef NS_ENUM(NSInteger, CYLSex) {
      CYLSexMan,
      CYLSexWoman
  };
```
```objc
4. #import @class
区别
1>  #import编译阶段拷贝"1.h"内容
    @class只是告诉编译器这是一个类,并不导入类里面的东西
2> 如果"1.h"改变,所有包含#import "1.h"的都要重新编译
    头文件相互包含3.h包含2.h, 2.h包含1.h, 改了1.h
    #import会导致重新编译2.h 3.h
    @class则只会重新编译2.h

使用:头文件@class引入类,.m文件再#import,使用类的相关属性和方法
```
```objc
5.循环retain
一端用retain,一端用assign
```
```objc
6.OC有没有私有方法,私有变量?
@interface Person : NSObject // 类声明
{
NSString *_test1; // 默认@protected,可以修改
@public NSString *_test2; // @public
}
@property (nonatomic, copy) NSString *test3; // @public 
@end

@interface Person () // 类扩展
{
NSString *_test1; // 默认@private
}
@property (nonatomic, copy) NSString *test5; // 默认是@private
@end

@implementation Person // 类实现
{
NSString *_test4; // 默认是@private,改不了
}
@end

分类中不能放实例变量
分类中的@property只会生成getter和setter方法的声明,没有下划线
变量
```


