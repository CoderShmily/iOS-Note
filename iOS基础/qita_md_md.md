# iOS基础 - 笔记

###什么情况使用weak关键字，相比assign有什么不同

1. 在 ARC 中,在有可能出现循环引用的时候,往往要通过让其中一端使用 weak 来解决,比如: delegate 代理属性
2. 自身已经对它进行一次强引用,没有必要再强引用一次,此时也会使用 weak,自定义 IBOutlet 控件属性一般也使用 weak；当然，也可以使用strong。

不同点：
1. weak 此特质表明该属性定义了一种“非拥有关系” (nonowning relationship)。为这种属性设置新值时，设置方法既不保留新值，也不释放旧值。此特质同assign类似， 然而在属性所指的对象遭到摧毁时，属性值也会清空(nil out)。 而 assign 的“设置方法”只会执行针对“纯量类型” (scalar type，例如 CGFloat 或 NSlnteger 等)的简单赋值操作。
2. assign 可以用非 OC 对象,而 weak 必须用于 OC 对象


---
### 怎么用 copy 关键字？

1. 用 @property 声明 NSString、NSArray、NSDictionary 经常使用 copy 关键字，是因为他们有对应的可变类型：NSMutableString、NSMutableArray、NSMutableDictionary，他们之间可能进行赋值操作，为确保对象中的字符串值不会无意间变动，应该在设置新属性值时拷贝一份。

2. block 也经常使用 copy 关键字. block 使用 copy 是从 MRC 遗留下来的“传统”,在 MRC 中,方法内部的 block 是在栈区的,使用 copy 可以把它放到堆区.在 ARC 中写不写都行：对于 block 使用 copy 还是 strong 效果是一样的，但写上 copy 也无伤大雅，还能时刻提醒我们：编译器自动对 block 进行了 copy 操作。


例如下面这个写法会出什么问题： 
```objc
@property (copy) NSMutableArray *array;
```
两个问题：
1. 添加,删除,修改数组内的元素的时候,程序会因为找不到对应的方法而崩溃.因为 copy 就是复制一个不可变 NSArray 的对象；
2. 使用了 atomic 属性会严重影响性能 ；一般情况下并不要求属性必须是“原子的”，因为这并不能保证“线程安全” ( thread safety)，若要实现“线程安全”的操作，还需采用更为深层的锁定机制才行。例如，一个线程在连续多次读取某属性值的过程中有别的线程在同时改写该值，那么即便将属性声明为 atomic，也还是会读到不同的属性值。
---
###block的声明
```objc
!rightBlock ? : rightBlock();

typedef NSString *(^completionBlock)(int a);
[... completionBlock:^NSString *(int a) {
         a = 10;
         return @"";
     }];
// 类型定义 block
typedef void(^CompletionBlock)(NSString *date);
@property (nonatomic, copy) CompletionBlock compBlock;

// 属性
@property (nonatomic,copy) void(^block)(NSString *str);

// 带参数的block  参数是是传给block调用者得
- (void)initBlock:(void (^)(NSString *str))block;
```
---
 ###枚举的写法 类型:CYLSex
```objc
  typedef NS_ENUM(NSInteger, CYLSex) {
      CYLSexMan,
      CYLSexWoman
  };
```
---
### #import 和 @class区别
```objc
1>  #import编译阶段拷贝"1.h"内容
    @class只是告诉编译器这是一个类,并不导入类里面的东西
2> 如果"1.h"改变,所有包含#import "1.h"的都要重新编译
    头文件相互包含3.h包含2.h, 2.h包含1.h, 改了1.h
    #import会导致重新编译2.h 3.h
    @class则只会重新编译2.h

使用:头文件@class引入类,.m文件再#import,使用类的相关属性和方法
```


---
### OC有没有私有方法,私有变量?
```objc
@interface Person : NSObject // 类声明
{
NSString *_test1; // 默认@protected,可以修改
@public NSString *_test2; // @public
}
@property (nonatomic, copy) NSString *test3; // @public 
@end

@interface Person () // 类扩展
{
NSString *_test1; // 默认@private,改不了
}
@property (nonatomic, copy) NSString *test5; // 默认是@private
@end

@implementation Person // 类实现
{
NSString *_test4; // 默认是@private,改不了
}
@end

分类中不能放实例变量
分类中的@property只会生成getter和setter方法的声明,没有生成下划线变量

1.@public (公开的)在有对象的前提下，任何地方都可以直接访问。
2.@protected （受保护的）只能在当前类和子类的对象方法中访问
3.@private （私有的）只能在当前类的对象方法中才能直接访问
4.@package (框架级别的)作用域介于私有和公开之间，只要处于同一个框架中就可以直接通过变量名问。
5.@interface中的声明的成员变量默认protected，属性默认是public,@implementation中声明的成员变量默认是private

方法有在@interface声明的默认public
只在类@implementation实现的是private
```

---


### 常用数据类型转换
```objc
NSURL *url2 = [NSURL fileURLWithPath:@"/a/b"]; // 已经添加了file协议,直接上路径
NSURL *url = [NSURL URLWithString:@"http://www.baidu.com:8080/search?id=1"]; // 要有http:// file:// 等协议头
NSLog(@"scheme:%@", [url scheme]); //协议 http
NSLog(@"host:%@", [url host]);     //域名 www.baidu.com
NSLog(@"absoluteString:%@", [url absoluteString]); //完整的url字符串   
NSLog(@"relativePath: %@", [url relativePath]); //相对路径 search
NSLog(@"port :%@", [url port]);  // 端口 8080
NSLog(@"path: %@", [url path]);  // 路径 search
NSLog(@"pathComponents:%@", [url pathComponents]); // search
NSLog(@"Query:%@", [url query]);  //参数 id=1

NSData *data = [NSData dataWithContentsOfURL:url];
NSData *data2 = [NSData dataWithContentsOfFile:@"path"];
NSData *data3 = [@"string" dataUsingEncoding:NSUTF8StringEncoding];

NSNumber *number = @'X';//字符型
NSNumber *number = @12.3;//双浮点型
NSNumber *number = @11111ll;//long long
NSNumber *number = @YES;//布尔值

// 把文件内容读取到字符串
NSString *str = [NSString stringWithContentsOfFile:@"path" encoding:NSUTF8StringEncoding error:nil];
// 把URL资源内容读取到字符串
NSString *str2 = [NSString stringWithContentsOfURL:url encoding:NSUTF8StringEncoding error:nil];
NSString *str = url.absoluteString; // 完整的URL字符串
// 写入字符串到文件,YES代表写入过程错误,不会生成文件
[@"string" writeToFile:@"/usr/test.txt" atomically:YES encoding:NSUTF8StringEncoding error:nil];
   
// NSData转换为UIImage
NSData *imageData = [NSData dataWithContentsOfFile: imagePath];
UIImage *image = [UIImage imageWithData: imageData];
   
// UIImage转换为NSData
NSData *data = UIImagePNGRepresentation(image);
NSData *imagedata=UIImageJEPGRepresentation(image,1.0);
[imagedata writeToFile:savedImagePath atomically:YES];
```
---






---

---

```objc
//
//                            _ooOoo_
//                           o8888888o
//                           88" . "88
//                           (| -_- |)
//                            O\ = /O
//                        ____/`---'\____
//                      .   ' \\| |// `.
//                       / \\||| : |||// \
//                     / _||||| -:- |||||- \
//                       | | \\\ - /// | |
//                     | \_| ''\---/'' | |
//                      \ .-\__ `-` ___/-. /
//                   ___`. .' /--.--\ `. . __
//                ."" '< `.___\_<|>_/___.' >'"".
//               | | : `- \`.;`\ _ /`;.`/ - ` : | |
//                 \ \ `-. \_ __\ /__ _/ .-` / /
//         ======`-.____`-.___\_____/___.-`____.-'======
//                            `=---='
//
//         .............................................
//                  佛祖保佑             永无BUG
//          佛曰:
//                  写字楼里写字间，写字间里程序员；
//                  程序人员写程序，又拿程序换酒钱。
//                  酒醒只在网上坐，酒醉还来网下眠；
//                  酒醉酒醒日复日，网上网下年复年。
//                  但愿老死电脑间，不愿鞠躬老板前；
//                  奔驰宝马贵者趣，公交自行程序员。
//                  别人笑我忒疯癫，我笑自己命太贱；
//                  不见满街漂亮妹，哪个归得程序员？

/**
 *
 * ━━━━━━神兽出没━━━━━━
 * 　　　┏┓　　　┏┓
 * 　　┏┛┻━━━┛┻┓
 * 　　┃　　　　　　　┃
 * 　　┃　　　━　　　┃
 * 　　┃　┳┛　┗┳　┃
 * 　　┃　　　　　　　┃
 * 　　┃　　　┻　　　┃
 * 　　┃　　　　　　　┃
 * 　　┗━┓　　　┏━┛Code is far away from bug with the animal protecting
 * 　　　　┃　　　┃    神兽保佑,代码无bug
 * 　　　　┃　　　┃
 * 　　　　┃　　　┗━━━┓
 * 　　　　┃　　　　　　　┣┓
 * 　　　　┃　　　　　　　┏┛
 * 　　　　┗┓┓┏━┳┓┏┛
 * 　　　　　┃┫┫　┃┫┫
 * 　　　　　┗┻┛　┗┻┛
 *
 * ━━━━━━感觉萌萌哒━━━━━━
 */

/**
 * 　　　　　　　　┏┓　　　┏┓
 * 　　　　　　　┏┛┻━━━┛┻┓
 * 　　　　　　　┃　　　　　　　┃
 * 　　　　　　　┃　　　━　　　┃
 * 　　　　　　　┃　＞　　　＜　┃
 * 　　　　　　　┃　　　　　　　┃
 * 　　　　　　　┃...　⌒　...　┃
 * 　　　　　　　┃　　　　　　　┃
 * 　　　　　　　┗━┓　　　┏━┛
 * 　　　　　　　　　┃　　　┃　Code is far away from bug with the animal protecting
 * 　　　　　　　　　┃　　　┃   神兽保佑,代码无bug
 * 　　　　　　　　　┃　　　┃
 * 　　　　　　　　　┃　　　┃
 * 　　　　　　　　　┃　　　┃
 * 　　　　　　　　　┃　　　┃
 * 　　　　　　　　　┃　　　┗━━━┓
 * 　　　　　　　　　┃　　　　　　　┣┓
 * 　　　　　　　　　┃　　　　　　　┏┛
 * 　　　　　　　　　┗┓┓┏━┳┓┏┛
 * 　　　　　　　　　　┃┫┫　┃┫┫
 * 　　　　　　　　　　┗┻┛　┗┻┛
 */

/**
 *　　　　　　　　┏┓　　　┏┓+ +
 *　　　　　　　┏┛┻━━━┛┻┓ + +
 *　　　　　　　┃　　　　　　　┃
 *　　　　　　　┃　　　━　　　┃ ++ + + +
 *　　　　　　 ████━████ ┃+
 *　　　　　　　┃　　　　　　　┃ +
 *　　　　　　　┃　　　┻　　　┃
 *　　　　　　　┃　　　　　　　┃ + +
 *　　　　　　　┗━┓　　　┏━┛
 *　　　　　　　　　┃　　　┃
 *　　　　　　　　　┃　　　┃ + + + +
 *　　　　　　　　　┃　　　┃　　　　Code is far away from bug with the animal protecting
 *　　　　　　　　　┃　　　┃ + 　　　　神兽保佑,代码无bug
 *　　　　　　　　　┃　　　┃
 *　　　　　　　　　┃　　　┃　　+
 *　　　　　　　　　┃　 　　┗━━━┓ + +
 *　　　　　　　　　┃ 　　　　　　　┣┓
 *　　　　　　　　　┃ 　　　　　　　┏┛
 *　　　　　　　　　┗┓┓┏━┳┓┏┛ + + + +
 *　　　　　　　　　　┃┫┫　┃┫┫
 *　　　　　　　　　　┗┻┛　┗┻┛+ + + +
 */
```
