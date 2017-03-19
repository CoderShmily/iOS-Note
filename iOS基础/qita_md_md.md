# iOS基础 - 笔记
---
 ###枚举的写法 
```objc
// 类型:CYLSex
typedef NS_ENUM(NSInteger, CYLSex) {
    CYLSexMan,
    CYLSexWoman
};


typedef NS_OPTIONS(NSUInteger, SDWebImageOptions) {

    SDWebImageRetryFailed = 1 << 0,
    SDWebImageScaleDownLargeImages = 1 << 1
};
```
---
###block的声明
```
block是不是一个对象?是一个对象

如何判断当前文件是MRC,还是ARC
1.dealloc 能否调用super,只有MRC才能调用super
2.能否使用retain,release.如果能用就是MRC

ARC管理原则:只要一个对象没有被强指针修饰就会被销毁,默认局部变量对象都是强指针,存放到堆里面

MRC了解开发常识:1.MRC没有strong,weak,局部变量对象就是相当于基本数据类型
              2.MRC给成员属性赋值,一定要使用set方法,不能直接访问下划线成员属性赋值

总结:只要block没有引用外部局部变量,block放在全局区

MRC:管理block
        只要Block引用外部局部变量,block放在栈里面.
        block只能使用copy,不能使用retain,使用retain,block还是在栈里面


ARC:管理block
    只要block引用外部局部变量,block放在堆里面
    block使用strong.最好不要使用copy
```

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







### const,static,extern简介

### 一、const与宏的区别（面试题）:
* `const简介`:之前常用的字符串常量，一般是抽成宏，但是苹果不推荐我们抽成宏，推荐我们使用const常量。
	* `执行时刻`:宏是预编译（编译之前处理），const是编译阶段。
	* `编译检查`:宏不做检查，不会报编译错误，只是替换，const会编译检查，会报编译错误。
	* `宏的好处`:宏能定义一些函数，方法。 const不能。
	* `宏的坏处`:使用大量宏，容易造成编译时间久，每次都需要重新替换。

```objc
// 常见的常量：抽成宏
#define XMGAccount @"account"

#define XMGUserDefault [NSUserDefaults standardUserDefaults]

// 字符串常量
static NSString * const account = @"account";

- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 偏好设置存储
    // 使用宏
    [XMGUserDefault setValue:@"123" forKey:XMGAccount];
    
    // 使用const常量
    [[NSUserDefaults standardUserDefaults] setValue:@"123" forKey:account];
    
}

```
	
### 二、const作用：限制类型
	*	1.const仅仅用来修饰右边的变量（基本数据变量p，指针变量*p）
	*	2.被const修饰的变量是只读的。

*  `const基本使用`

```objc
- (void)viewDidLoad {
    [super viewDidLoad];
    
    // 定义变量
    int a = 1;
    
    // 允许修改值
    a = 20;
    
    // const两种用法
    // const:修饰基本变量p
    // 这两种写法是一样的，const只修饰右边的基本变量b
    const int b = 20; // b:只读变量
    int const b = 20; // b:只读变量
    
    // 不允许修改值
    b = 1;
    
    // const:修饰指针变量*p，带*的变量，就是指针变量.
    // 定义一个指向int类型的指针变量，指向a的地址
    int *p = &a;
    
    int c = 10;
    
    p = &c;
    
    // 允许修改p指向的地址，
    // 允许修改p访问内存空间的值
    *p = 20;
    
    // const修饰指针变量访问的内存空间，修饰的是右边*p1，
    // 两种方式一样
    const int *p1; // *p1：常量 p1:变量
    int const *p1; // *p1：常量 p1:变量
    
    // const修饰指针变量p1
    int * const p1; // *p1:变量 p1:常量

    
    // 第一个const修饰*p1 第二个const修饰 p1
    // 两种方式一样
    const int * const p1; // *p1：常量 p1：常量
    
    int const * const p1;  // *p1：常量 p1：常量
    
    
    
}

```


### 三、const开发中使用场景:
	*	1.需求1:提供一个方法，这个方法的参数是地址，里面只能通过地址读取值,不能通过地址修改值
	*	2.需求2:提供一个方法，这个方法的参数是地址，里面不能修改参数的地址。

```objc
@implementation ViewController

// const放*前面约束参数，表示*a只读
// 只能修改地址a,不能通过a修改访问的内存空间
- (void)test:(const int * )a
{
//    *a = 20;
}

// const放*后面约束参数，表示a只读
// 不能修改a的地址，只能修改a访问的值
- (void)test1:(int * const)a
{
    int b;
    // 会报错
    a = &b;
    
    *a = 2;
}

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    
    int a = 10;
    
    // 需求1:提供一个方法，这个方法的参数是地址，里面只能通过地址读取值,不能通过地址修改值。
    
    // 这时候就需要使用const，约束方法的参数只读.
    [self test:&a];
    
    // 需求2:提供一个方法，这个方法的参数是地址，里面不能修改参数的地址。
    [self test1:&a];
}


@end
```

### 四、static和extern简单使用(要使用一个东西，先了解其作用)

*	`static作用`:
 	* 修饰局部变量：
 	
 		1.延长局部变量的生命周期,程序结束才会销毁。
 		
		2.局部变量只会生成一份内存,只会初始化一次。
		 
		3.改变局部变量的作用域。
 
	 * 修饰全局变量
	 
 		1.只能在本文件中访问,修改全局变量的作用域,生命周期不会改
 
		2.避免重复定义全局变量
 
 
 * 	`extern作用`:
 	*	只是用来获取全局变量(包括全局静态变量)的值，不能用于定义变量
 *	`extern工作原理`:
 	* 	先在当前文件查找有没有全局变量，没有找到，才会去其他文件查找。
 	
```objc
// 全局变量：只有一份内存，所有文件共享，与extern联合使用。
int a = 20;

// static修饰全局变量
static int age = 20;

- (void)test
{
    // static修饰局部变量
    static int age = 0;
    age++;
    NSLog(@"%d",age);
}

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    
    
    [self test];
    [self test];
    
    extern int age;
    NSLog(@"%d",age);
}
I
```

### 五、static与const联合使用
*	static与const作用:声明一个只读的静态变量
*	开发使用场景:在`一个文件中`经常使用的字符串常量，可以使用static与const组合

```objc
// 开发中常用static修饰全局变量,只改变作用域

// 为什么要改变全局变量作用域，防止重复声明全局变量。

// 开发中声明的全局变量，有些不希望外界改动，只允许读取。

// 比如一个基本数据类型不希望别人改动

// 声明一个静态的全局只读常量
static const int a = 20;

// staic和const联合的作用:声明一个静态的全局只读常量

// iOS中staic和const常用使用场景，是用来代替宏，把一个经常使用的字符串常量，定义成静态全局只读变量.

// 开发中经常拿到key修改值，因此用const修饰key,表示key只读，不允许修改。
static  NSString * const key = @"name";

// 如果 const修饰 *key1,表示*key1只读，key1还是能改变。

static  NSString const *key1 = @"name";

```


### 六、extern与const联合使用
*	开发中使用场景:在`多个文件中`经常使用的同一个字符串常量，可以使用extern与const组合。
*	原因:
	*	static与const组合：在每个文件都需要定义一份静态全局变量。
	*	extern与const组合:只需要定义一份全局变量，多个文件共享。
*  全局常量正规写法:开发中便于管理所有的全局变量，通常搞一个GlobeConst文件，里面专门定义全局变量，统一管理，要不然项目文件多不好找。


* GlobeConst.h

```objc
/*******************************首页****************************/

extern NSString * const nameKey = @"name";

/*******************************首页****************************/
```

* GlobeConst.m

```objc
#import <Foundation/Foundation.h>

/*******************************首页****************************/

NSString * const nameKey = @"name";


/*******************************首页****************************/

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
