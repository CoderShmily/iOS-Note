
```objc
@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.

    // 1.创建窗口
    self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];

    // 2.加载storyboard文件，创建控制器

    // name:就是storyboard文件名
    // bundle:主bundle,传入nil，表示主bundle
    UIStoryboard *storyboard = [UIStoryboard storyboardWithName:@"Main" bundle:nil];

    // 通过storyboard对象初始化指定的控制器
    // UIViewController *rootVc = [storyboard instantiateViewControllerWithIdentifier:@"vc"];

    // instantiateInitialViewController：加载storyboard箭头指向的控制器
    UIViewController *rootVc = [storyboard instantiateInitialViewController];

    // 3.设置窗口的根控制器，并且显示窗口
    self.window.rootViewController = rootVc;

    // 4.显示窗口
    [self.window makeKeyAndVisible];

    return YES;
}

@end
```
