# iOS基础 - 笔记
---
 ###枚举的写法 
```objc
// 类型:CYLSex
typedef NS_ENUM(NSInteger, CYLSex) {
    CYLSexMan,
    CYLSexWoman
};typedef NS_OPTIONS(NSUInteger, SDWebImageOptions) {

    SDWebImageRetryFailed = 1 << 0,
    SDWebImageScaleDownLargeImages = 1 << 1
};

typedef NS_OPTIONS(NSUInteger, SDWebImageOptions) {

    SDWebImageRetryFailed = 1 << 0,
    SDWebImageScaleDownLargeImages = 1 << 1
};
```
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
###什么情况使用weak关键字，相比assign有什么不同

1. 在 ARC 中,在有可能出现循环引用的时候,往往要通过让其中一端使用 weak 来解决,比如: delegate 代理属性
2. 自身已经对它进行一次强引用,没有必要再强引用一次,此时也会使用 weak,自定义 IBOutlet 控件属性一般也使用 weak；当然，也可以使用strong。

不同点：
1. weak 此特质表明该属性定义了一种“非拥有关系” (nonowning relationship)。为这种属性设置新值时，设置方法既不保留新值，也不释放旧值。此特质同assign类似， 然而在属性所指的对象遭到摧毁时，属性值也会清空(nil out)。 而 assign 的“设置方法”只会执行针对“纯量类型” (scalar type，例如 CGFloat 或 NSlnteger 等)的简单赋值操作。
2. assign 可以用非 OC 对象,而 weak 必须用于 OC 对象

---
### weak的其他问题
###### 1. weak属性需要在dealloc中置nil么？
> 不需要。在ARC环境无论是强指针还是弱指针都无需在 dealloc 设置为 nil ， ARC 会自动帮我们处理。根据`runtime实现 weak 属性方式`，即便是编译器不帮我们做这些，weak也不需要在 dealloc 中置nil。

正如上文的： 中提到的：

###### 2. weak的底层实现
> runtime 对注册的类， 会进行布局，对于 weak 对象会放入一个 hash 表中。 用 weak 指向的对象内存地址作为 key，当此对象的引用计数为0的时候会 dealloc，假如 weak 指向的对象内存地址是a，那么就会以a为键， 在这个 weak 表中搜索，找到所有以a为键的 weak 对象，从而设置为 nil。

---
### 怎么用 copy 关键字？

1. 用 @property 声明 NSString、NSArray、NSDictionary 经常使用 copy 关键字，是因为他们有对应的可变类型：NSMutableString、NSMutableArray、NSMutableDictionary，他们之间可能进行赋值操作，为确保对象中的字符串值不会无意间变动，应该在设置新属性值时拷贝一份。

2. block 也经常使用 copy 关键字. block 使用 copy 是从 MRC 遗留下来的“传统”,在 MRC 中,方法内部的 block 是在栈区的,使用 copy 可以把它放到堆区.在 ARC 中写不写都行：对于 block 使用 copy 还是 strong 效果是一样的，但写上 copy 也无伤大雅，还能时刻提醒我们：编译器自动对 block 进行了 copy 操作。


###### 下面这个写法会出什么问题： 
```objc
@property (copy) NSMutableArray *array;
```
两个问题：
1. 添加,删除,修改数组内的元素的时候,程序会因为找不到对应的方法而崩溃.因为 copy 就是复制一个不可变 NSArray 的对象；
2. 使用了 atomic 属性会严重影响性能 ；一般情况下并不要求属性必须是“原子的”，因为这并不能保证“线程安全” ( thread safety)，若要实现“线程安全”的操作，还需采用更为深层的锁定机制才行。例如，一个线程在连续多次读取某属性值的过程中有别的线程在同时改写该值，那么即便将属性声明为 atomic，也还是会读到不同的属性值。


###### 总结copy的浅复制和深复制
在集合类对象中，对 immutable 对象进行 copy，是指针复制， mutableCopy 是内容复制；对 mutable 对象进行 copy 和 mutableCopy 都是内容复制。但是：集合对象的内容复制仅限于对象本身，对象元素仍然是指针复制。用代码简单表示如下：
```objc
[immutableObject copy] // 浅复制
[immutableObject mutableCopy] //单层深复制
[mutableObject copy] //单层深复制
[mutableObject mutableCopy] //单层深复制
```

---

### @synthesize和@dynamic分别有什么作用？
1. @property有两个对应的词，一个是 @synthesize，一个是 @dynamic。如果 @synthesize和 @dynamic都没写，那么默认的就是`@syntheszie var = _var`;
2. @synthesize 的语义是如果你没有手动实现 setter 方法和 getter 方法，那么编译器会自动为你加上这两个方法。
3. @dynamic 告诉编译器：属性的 setter 与 getter 方法由用户自己实现，不自动生成。（当然对于 readonly 的属性只需提供 getter 即可）。假如一个属性被声明为 @dynamic var，然后你没有提供 @setter方法和 @getter 方法，编译的时候没问题，但是当程序运行到 `instance.var = someVar`，由于缺 setter 方法会导致程序崩溃；或者当运行到 `someVar = var` 时，由于缺 getter 方法同样会导致崩溃。编译时没问题，运行时才执行相应的方法，这就是所谓的动态绑定。

#####总结下 @synthesize 合成实例变量的规则，有以下几点：

1. 如果指定了成员变量的名称,会生成一个指定的名称的成员变量,如果这个成员已经存在了就不再生成了.

2. 如果是 @synthesize foo; 还会生成一个名称为foo的成员变量，也就是说：如果没有指定成员变量的名称会自动生成一个属性同名的成员变量,

3. 如果是 @synthesize foo = _foo; 就不会生成成员变量

4. 假如 property 名为 foo，存在一个名为 _foo 的实例变量，那么还会自动合成新变量么？ 不会,Xcode会有警告提示。
---

###什么情况下不会autosynthesis（自动合成）？

1. 同时重写了 setter 和 getter 时
2. 重写了只读属性的 getter 时
3. 使用了 @dynamic 时
4. 在 @protocol 中定义的所有属性
5. 在 category 中定义的所有属性
6. 重载的属性，当你在子类中重载了父类中的属性，如果不去手动定义ivar,那么就要使用 @synthesize 来手动合成ivar。

>除了后三条，对其他几个我们可以总结出一个规律：当你想手动管理 @property 的所有内容时，你就会尝试通过实现 @property 的所有“存取方法”（the accessor methods）或者使用 @dynamic 来达到这个目的，这时编译器就会认为你打算手动管理 @property，于是编译器就禁用了 autosynthesis（自动合成）。

---

### #import 和 @class区别
1. \#import编译阶段拷贝"1.h"内容
    @class只是告诉编译器这是一个类,并不导入类里面的东西
2. 如果"1.h"改变,所有包含#import "1.h"的都要重新编译
    ```objc
    #import3.h
    #import2.h
    #import1.h
    
    #import2.h
    #import1.h
    
    /*
    #import会导致重新编译2.h、3.h
    @class则只会重新编译2.h
    使用方法:头文件@class引入类,.m文件再#import,使用类的相关属性和方法
    */
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

```
分类中不能放实例变量,分类中的`@property`只会生成getter和setter方法的声明,没有生成下划线变量

1. `@public` (公开的)在有对象的前提下，任何地方都可以直接访问。
2. `@protected` （受保护的）只能在当前类和子类的对象方法中访问
3. `@private` （私有的）只能在当前类的对象方法中才能直接访问
4. `@package` (框架级别的)作用域介于私有和公开之间，只要处于同一个框架中就可以直接通过变量名问。
5. @interface中的声明的成员变量默认`protected`，属性默认是`public`,@implementation中声明的成员变量默认是`private`
6. 方法有在@interface声明的默认`public`，只在类@implementation实现的是`private`
---

### @protocol 和 category 中如何使用 @property
1. 在 protocol 中使用 property 只会生成 setter 和 getter 方法声明,我们使用属性的目的,是希望遵守我协议的对象能实现该属性

2. category 使用 @property 也是只会生成 setter 和 getter 方法的声明,如果我们真的需要给 category 增加属性的实现,需要借助于运行时的两个函数：
  1. objc_setAssociatedObject
  2. objc_getAssociatedObject
  
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
