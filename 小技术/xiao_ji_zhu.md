<font color=red> github不可用 </font>
<a href="#1">查找框架</a>  
<a name="1">锚点1跳转处</a>
<a href="#2">锚点2</a>  
<a id="2">锚点2跳转处 二级标题</a>
[hello`这里面可加修饰符`](https://github.com/CoderShmily/iOS-Note/blob/master/iOS基础/cocoapods.md#1)

## 目录
- <a href="#文件路径设置">pch文件路径设置</a>
- <a href="#字符串比较">字符串比</a>
- <a href="#获取窗口当前显示的控制器">获取窗口当前显示的控制器</a>
- <a href="#IOS在不打开电话服务的时候，可以响应服务器的推送消息">IOS在不打开电话服务的时候，可以响应服务器的推送消息</a>
- <a href="判断VIEW是否显示在屏幕上">判断View是否显示在屏幕上</a>
 - <a href="UIView遮盖层">UIView 遮盖层，中间部分区域透明可视</a>






### <a name="文件路径设置">pch文件路径设置</a>
在Build Settings 找Prefix Header
- pch文件使用前要包含路径
    - 从工程目录下开始所以可省略为 00000/PrefixHeader.pch
    - 还有个$(SRCROOT)/pchFile.pch什么的...

### <a name="字符串比较">字符串比较</a>
- 常用的几个compre 方法

```objc
[value compare:(NSString *)];
[value compare:(NSString *) options:(NSStringCompareOptions)];
[value compare:(NSString *) options:(NSStringCompareOptions) range:(NSRange)];

# 参数解释
1. options:枚举
enum NSStringCompareOptions{
    NSCaseInsensitiveSearch = 1,//不区分大小写比较
    NSLiteralSearch = 2,//区分大小写比较
    NSBackwardsSearch = 4,//从字符串末尾开始搜索
    NSAnchoredSearch = 8,//搜索限制范围的字符串
    NSNumbericSearch = 64, //按照字符串里的数字为依据，算出顺序。例如 Foo2.txt < Foo7.txt < Foo25.txt

    //以下定义高于 mac os 10.5 或者高于 iphone 2.0 可用
    NSDiacriticInsensitiveSearch = 128,//忽略 "-" 符号的比较
    NSWidthInsensitiveSearch = 256,//忽略字符串的长度，比较出结果
    NSForcedOrderingSearch = 512,//忽略不区分大小写比较的选项，并强制返回 NSOrderedAscending 或者 NSOrderedDescending
    
    //以下定义高于 iphone 3.2 可用
    NSRegularExpressionSearch = 1024,//只能应用于 rangeOfString:..., stringByReplacingOccurrencesOfString:...和 replaceOccurrencesOfString:... 方法。使用通用兼容的比较方法，如果设置此项，可以去掉 NSCaseInsensitiveSearch 和 NSAnchoredSearch
}

2. range:比较字符串的范围
结构变量：
location: 需要比较的字串起始位置（以0为起始）
length: 需要比较的字串长度
 
3. 返回值：
enum NSComparisonResult {
     NSOrderedAscending = -1,    // < 升序
     NSOrderedSame,              // = 等于
     NSOrderedDescending   // > 降序
} NSComparisonResult;
```

### <a name="获取窗口当前显示的控制器">获取窗口当前显示的控制器</a> 
```objc
解决类似网易新闻客户端收到新闻推送后，弹出一个UIAlert，然后跳转到新闻详情页面这种需求

1.提供一个UIView的分类方法，这个方法通过响应者链条获取view所在的控制器
- (UIViewController *)parentController
{
    UIResponder *responder = [self nextResponder];
    while (responder) {
        if ([responder isKindOfClass:[UIViewController class]]) {
            return (UIViewController *)responder;
        }
        responder = [responder nextResponder];
    }
    return nil;
}
2.通过控制器的布局视图可以获取到控制器实例对象,modal的展现方式需要取到控制器的根视图
+ (UIViewController *)currentViewController
{
    UIWindow *keyWindow = [UIapplication sharedApplication].keyWindow;
    // modal展现方式的底层视图不同
    // 取到第一层时，取到的是UITransitionView，通过这个view拿不到控制器
    UIView *firstView = [keyWindow.subviews firstObject];
    UIView *secondView = [firstView.subviews firstObject];
    UIViewController *vc = secondView.parentController;
    if ([vc isKindOfClass:[UITabBarController class]]) {
        UITabBarController *tab = (UITabBarController *)vc;
        if ([tab.selectedViewController isKindOfClass:[UINavigationController class]]) {
            UINavigationController *nav = (UINavigationController *)tab.selectedViewController;
            return [nav.viewControllers lastObject];
        } else {
            return tab.selectedViewController;
        }
    } else if ([vc isKindOfClass:[UINavigationController class]]) {
        UINavigationController *nav = (UINavigationController *)vc;
        return [nav.viewControllers lastObject];
    } else {
        return vc;
    }
    return nil;
}
```
### <a name="IOS在不打开电话服务的时候，可以响应服务器的推送消息">IOS在不打开电话服务的时候，可以响应服务器的推送消息</a> 
在做即时通讯（基于xmpp框架）的时候遇到这样一个问题，就是在真机测试的时候，你按Home键返回桌面
在你返回桌面的时候，这是你的程序的挂起状态的，在你挂起的时候，相当于你的程序是死的，程序的所有进程全部是睡眠状态，所有这时候你做任何操作，都是无用的,手机永远也接收不到你的任何消息推送，因为在这里，手机处于休眠状态，这时候你必须要把他唤醒，才能正常的接收你所推送过来的消息.当时我的应用是打开了电话服务，因为电话服务永远都是让手机保持唤醒状态，所有在你手机处于任何状态的时候，都可以接到电话的，所有这就是电话服务。我当时用的就是这样的一个服务，使我的程序不被睡眠，当我上架AppStore的时候，被苹果拒绝了，所有绞尽脑汁想到了一个解决方法.如图是添加的电话服务，在我添加这样的服务时候，苹果商店不让上架AppStore的。所以我想了别的办法
```objc
在AppDelegate中。添加上这么一段代码即可。
- (void)applicationDidEnterBackground:(UIApplication *)application
 {
     [[UIApplication sharedApplication]setApplicationIconBadgeNumber:0];//进入前台取消应用消息图标    
     UIApplication*   app = [UIApplication sharedApplication];     
     __block    UIBackgroundTaskIdentifier bgTask;
     bgTask = [app beginBackgroundTaskWithExpirationHandler:^{
         
         dispatch_async(dispatch_get_main_queue(), ^{
             if (bgTask != UIBackgroundTaskInvalid)  
             { 
                 bgTask = UIBackgroundTaskInvalid; 
             } 
         }); 
     }];
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
         dispatch_async(dispatch_get_main_queue(), ^{
             if (bgTask != UIBackgroundTaskInvalid)  
             {  
                 bgTask = UIBackgroundTaskInvalid; 
             } 
         });  
     });
 }
```
### <a name="判断VIEW是否显示在屏幕上">判断View是否显示在屏幕上</a>
```objc
@implementation UIView (UIScreenDisplaying)
 
// 判断View是否显示在屏幕上
- (BOOL)isDisplayedInScreen
{
    if (self == nil) {
        return FALSE;
    }
    CGRect screenRect = [UIScreen mainScreen].bounds;
    // 转换view对应window的Rect
    CGRect rect = [self convertRect:self.frame fromView:nil];
    if (CGRectIsEmpty(rect) || CGRectIsNull(rect)) {
        return FALSE;
    }
    // 若view 隐藏
    if (self.hidden) {
        return FALSE;
    }  
    // 若没有superview
    if (self.superview == nil) {
        return FALSE;
    }
    // 若size为CGrectZero
    if (CGSizeEqualToSize(rect.size, CGSizeZero)) {
        return  FALSE;
    }
    // 获取 该view与window 交叉的 Rect
    CGRect intersectionRect = CGRectIntersection(rect, screenRect);
    if (CGRectIsEmpty(intersectionRect) || CGRectIsNull(intersectionRect)) {
        return FALSE;
    } 
    return TRUE;
}
```


### <a name="UIView遮盖层">UIView 遮盖层，中间部分区域透明可视</a>

```objc
第一张图的方法：

//新建testview
    UIImage *image = [UIImageimageNamed:@"photo3.jpg"];
    [self.view.layersetContents:(id)[imageCGImage]];

    testView *testVW = [[testViewalloc]initWithFrame:self.view.frame];
    testVW.backgroundColor = [UIColorclearColor];
    testVW.opaque =NO;
    [self.viewaddSubview:testVW];
    // view 的背景色一定要设置成clearColor,opaue一定要设置no;
   // 在testView的类中实现；
   
- (void)drawRect:(CGRect)rect {
    // Start by filling the area with the blue color
    [[UIColor colorWithWhite:0.0f alpha:0.5f] setFill];//阴影效果 根据透明度来设计
    UIRectFill( rect );
    CGRect holeRectIntersection = CGRectIntersection( holeRect, rect );
    [[UIColor clearColor] setFill];
    UIRectFill( holeRectIntersection );
}

方法二：
    CGRect myRect =CGRectMake(100,100,200, 200);
    int radius = myRect.size.width/2.0;
    UIBezierPath *path = [UIBezierPathbezierPathWithRoundedRect:CGRectMake(0,0, backView.bounds.size.width, backView.bounds.size.height)cornerRadius:0];
    UIBezierPath *circlePath = [UIBezierPathbezierPathWithRoundedRect:CGRectMake(100,100,2.0*radius,2.0*radius)cornerRadius:radius];
    [path appendPath:circlePath];
    [path setUsesEvenOddFillRule:YES];
    
    CAShapeLayer *fillLayer = [CAShapeLayerlayer];
    fillLayer.path = path.CGPath;
    fillLayer.fillRule =kCAFillRuleEvenOdd;
    fillLayer.fillColor = [UIColorgrayColor].CGColor;
    fillLayer.opacity =0.5;
    [backView.layeraddSublayer:fillLayer];
```
```objc
xcode5（iOS7）? image.xcassets 里面有个lunch文件夹放图片

图片拉伸代码有三种 xcode有三种?
```

## 多线程
iPhone OS下的主线程的堆栈大小是1M，第二个线程开始就是512KB，并且该值不能通过编译器开关或线程API函数来更改，只有主线程有直接修改UI的能力

即：主线程最大占1M的栈区空间,每条子线程最大占512K的栈区空间

iOS多线程的应用场景
- `单例模式`
- `可以将耗时的执行路径(如网络请求)放在其他线程中执行`
- 

## GCD



 ##NSURLSession 和 NSURLConnection
 当程序在前台时,NSURLSession和NSURLConnection大部分可以互相替代.
 
- `NSURLSession支持后台网络操作,除非用户强行关闭`.

- `session支持后台有block等`

- `NSURLsession最直接的改进就是可以配置每个session的缓存，协议，cookie，以及证书策略（credential policy），甚至跨程序共享这些信息。`
这将允许程序和网络基础框架之间相互独立，不会发生干扰。每个NSURLSession对象都由一个NSURLSessionConfiguration对象来进行初始化，后者指定了刚才提到的那些策略以及一些用来增强移动设备上性能的新选项。

- 相比较NSURLConnection的返回处理，NSURLSession提供了灵活的数据返回方式，可以使用简单的block方式来处理返回数据，也可以使用更强大的delegate.

NSURLSession提供的功能:

    1> 通过URL将数据下载到内存;    

    2> 通过URL将数据下载到文件系统;

    3> 将数据上传到指定的URL;

    4> 在后台完成上述功能.

    5> 支持下载,断点续传,后台上传/下载,后台上传/下载任务跟进
    
NSURLSessionTask

    NSURLSession使用NSURLSessionTask来具体执行网络请求的任务.

    NSURLSessionTask支持网络请求的取消、暂停和恢复，比如下载文件暂停之后再恢复就能够自动从上次的进度继续下载 .

    NSURLSessionTask还能获取数据的读取进度 .

    NSURLSessionTask的三种类型:

    1> NSURLSessionDataTask 处理一般的NSData数据对象, 比如通过GET或POST方式从服务器获取JSON或XML返回等等, 但不支持后台获取.

    2> NSURLSessionUploadTask 用于上传文件, 支持后台上传 .

    3> NSURLSessionDownloadTask 用于下载文件, 支持后台下载 .
    
    
    
## iOS设计模式
设计模式的好处：我们可以写出容易理解，重用度很高的代码。降低代码的耦合度，符合软件工程的思想。

设计模式主要分为三类：
- `创造型的`：`单例`和`抽象工厂`。
- `结构类型的`：`MVC`  Decorator, Adapter, Facade and Composite.等这几种模式
- `行为性的`：`Observer`, Memento, Chain of Responsibility and Command.

#### iOS设计模式（常用的）
- `单例设计模式`
- `装饰设计模式的代理设计模式`,装饰设计模式动态的添加行为和责任向一个对象而不修改他的任何代码
- `装饰设计模式的类别设计模式`
- `观察者设计模式`
- 
