### 一、加载控制器的View
```objc
#import "ViewController.h"

@interface ViewController ()

@end

@implementation ViewController

// 作用：加载控制器的view
// 会调用父类（UIViewController）方法[super loadView]
// 会调用系统默认的做法
// 注意：一旦重写了loadView，就不要调用[super loadView]

/*
什么时候调用，第一次使用控制器的View的时候
什么时候需要重写loadView: 自定义控制器的View就调用这个方法
控制器的View想展示一张图片
UIWebView
*/
- (void)loadView // 重写loadView
{
   UIView *vcView = [[UIView alloc] initWithFrame:[UIScreen mainScreen].bounds];
    vcView.backgroundColor = [UIColor yellowColor];

    self.view = vcView;
}
/*
- (UIView *)view // view的get方法
{
    if (_view == nil) {
        [self loadView];
    }
    return _view;
}
*/

@end
```
### 二、控制器的View的创建
```objc
@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.

    self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];

//    UIStoryboard *storyboard = [UIStoryboard storyboardWithName:@"Main" bundle:nil];
//
//    ViewController *rootVc = [storyboard instantiateInitialViewController];

    // 假设通过Xib创建XMGViewController控制器对象
    // 1.如果nibName为nil,首先会去查找XMGView.xib,如果有，就会去加载XMGView.xib描述的控制器的view
    // 2.如果没有找到，就会去找XMGViewController.xib,如果有，也会去加载
    // 3.都没有找到，就会生成一个空的view

    // init底层就会调用initWithNibName:bundle:
    XMGViewController *vc = [[XMGViewController alloc] initWithNibName:nil bundle:nil];

    self.window.rootViewController = vc;

    [self.window makeKeyAndVisible];

    return YES;
}
@end


@implementation XMGViewController

- (instancetype)initWithNibName:(NSString *)nibNameOrNil bundle:(NSBundle *)nibBundleOrNil
{
    NSLog(@"%s",__func__);

    return [super initWithNibName:nibNameOrNil bundle:nibBundleOrNil];
}
// 只要没有重写loadView，系统就会自动判断有没有storyboard或者xib描述控制器的View,如果有就会去加载它们描述控制器的view

```
### 三、 系统默认创建的View
```objc
    // 1. 创建窗口
    self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];
    self.window.backgroundColor = [UIColor redColor];

    UIButton *button = [UIButton buttonWithType:UIButtonTypeSystem];
    button.frame = CGRectMake(0, 0, 100, 100);
    button.backgroundColor = [UIColor whiteColor];
    [button setTitle:@"粗来" forState:UIControlStateNormal];
    [self.window addSubview: button];

    // 2. 手动加载xib
    UIViewController *control = [[UIViewController alloc] initWithNibName:nil bundle:nil];
// 加载的nib为nil时，默认会给控制器产生一个clear颜色的View，会阻挡windows上的控件的点击事件
// clear的alpha估计在0.02~1之间
    control.view.alpha = 0.5; // 0~0.01 windows上添加的按钮可点击
                              // 0.02 开始按钮不可点击

    // 3. 用storyboard创建主控制器，并设置为根控制
    self.window.rootViewController =  control;
    // 4. 显示窗口
    self.window.hidden = NO;

    // 1. xib要有view
    // 2. xib的file owner的Class name要设置控制器UIViewController / ViewController
    // 3. xib的view连线到控件View
```
#### 四、控制器的view延迟加载
```objc
@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.

    self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];
    // 创建控制器的View,在创建控制器的时候，并不会加载控制器的view
    ViewController *vc = [[ViewController alloc] init];

    // UIViewController控制器的View第一次使用的时候加载
    vc.view.backgroundColor = [UIColor redColor];

    self.window.rootViewController = vc;
    // 显示窗口，窗口上的东西也需要显示
    [self.window makeKeyAndVisible];

    return YES;
}
@end
```
```objc
#import "ViewController.h"

@interface ViewController ()

@end

@implementation ViewController
//- (UIView *)view
//{
//    if (_view == nil) {
//        [self loadView];
//        [self viewDidLoad];
          ...
//    }
//    return _view;
//}
- (void)loadView
{
    // 如果当前控制器是窗口的根控制器，可以不设置尺寸。
    UIView *greenView = [[UIView alloc] init];

    greenView.backgroundColor = [UIColor greenColor];

    self.view = greenView;
}

// 控制器的View加载完成的时候调用
- (void)viewDidLoad {
    [super viewDidLoad];

    self.view.backgroundColor = [UIColor yellowColor];

    // 以后打印控制器的View真实尺寸，一般不再viewDidLoad去打印，因为不准确
}

- (void)viewDidAppear:(BOOL)animated
{
    [super viewDidAppear:animated];
    NSLog(@"%@",NSStringFromCGRect(self.view.frame));
}
```
