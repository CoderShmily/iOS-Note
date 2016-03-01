# iOS基础 - 笔记
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

<font color=red> 内容 </font>
<a href="#1">查找框架</a>  
<a name="1">锚点1跳转处</a>

<a href="#2">锚点2</a>  
<h2 id="2">锚点2跳转处</h2>
[hello`这里面可加修饰符`](https://cocoapods.md#1)
[hello`这里面可加修饰符`](https://1.md#25w)


```objc
1. [super dealloc]; // 要调用super的此方法,要在MRC下
```
```objc
2. @property (nonatomic, copy, getter=isRich) BOOL rich; // 指定getter方法

@implementation ViewController
@synthesize var = _var; // 指定生成下划线变量
@end
```
```objc
3. 枚举的写法 类型:CYLSex
  typedef NS_ENUM(NSInteger, CYLSex) {
      CYLSexMan,
      CYLSexWoman
  };
```
```objc
4. #import 和 @class区别

1>  #import编译阶段拷贝"1.h"内容
    @class只是告诉编译器这是一个类,并不导入类里面的东西
2> 如果"1.h"改变,所有包含#import "1.h"的都要重新编译
    头文件相互包含3.h包含2.h, 2.h包含1.h, 改了1.h
    #import会导致重新编译2.h 3.h
    @class则只会重新编译2.h

使用:头文件@class引入类,.m文件再#import,使用类的相关属性和方法
```
```objc
5. 循环retain
一端用retain,一端用assign
```
```objc
6. OC有没有私有方法,私有变量?
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

```objc
7. block的定义

int (^block)(int ,int) = ^(int a, int b)
{
	return 0;
}
// 类型重定义
typedef int (^MyBlock)(int ,int);

```

```objc
8. [error localizedDescription]; // 精确输出error的错误,没有其他乱七八糟的
```
```objc
9. 常用数据类型转换

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
