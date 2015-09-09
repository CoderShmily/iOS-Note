```objc
@implementation AppDelegate
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    // 1.创建窗口
    self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];

    // 2.设置窗口的根控制器
    // 通过xib去加载控制器
    // 1.首先创建一个Xib文件
    // 2.Xib文件需要拖一个View描述控制器的View
    // 3.需要把Xib上的View与控制器连线，设置Xib的File'owner的class为控制器
    UIViewController *rootVc = [[UIViewController alloc] initWithNibName:@"VC" bundle:nil];
    self.window.rootViewController = rootVc;


    // 3.显示窗口
    [self.window makeKeyAndVisible];

    return YES;
}

@end
```
