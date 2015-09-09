### 一、导航控制器

###### 1. 导航控制器的创建
-  如果设置导航控制器的根控制器，就会把跟控制器的view添加到导航控制器的View
-  导航控制器必须要有根控制器
```objc
@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.

    self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];

    // 创建导航控制器的根控制器

    UIViewController *vc = [[UIStoryboard storyboardWithName:@"Main" bundle:nil] instantiateInitialViewController];

    // 创建导航控制器，设置根控制器
    UINavigationController *nav = [[UINavigationController alloc] initWithRootViewController:vc];

/* // 第一个添加的控制器也可以成为导航控制器的根控制器
    UINavigationController *nav = [[UINavigationController alloc] init];
    [nav pushViewController:vc animated:YES];
*/

    self.window.rootViewController = nav;

    [self.window makeKeyAndVisible];

    return YES;
}
@end
```
2.导航控制器的跳转

<1> 如果一个控制器是导航控制器的子控制器，就可以拿到导航控制器
<2> 导航控制器的viewControllers数组对象保存了所有子控制器
<3> next跳转不能用viewControllers获得控制器对象，因为还没创建
    back跳转可以用viewControllers数组获取控制器对象
<4> 跳转到上一个控制器后，此控制器在跳转动画后销毁

```objc
@implementation ViewController

- (IBAction)jump2Two:(id)sender {
    // init 也会调用initWithNibName: 传入参数为空
    // 会去查找View.xib ViewControl.xib ...默认创建
    TwoViewController *twoVC = [[TwoViewController alloc] init];

    [self.navigationController pushViewController:twoVC animated:YES];

}

- (IBAction)jumpback:(id)sender {

    // 跳转到上一个界面调用popViewControllerAnimated
    [self.navigationController popViewControllerAnimated:YES];

}

- (IBAction)backRoot:(id)sender {  // 回到根控制器
//    self.navigationController.viewControllers;
//    NSLog(@"%@",self.navigationController.viewControllers);

    [self.navigationController popToViewController:self.navigationController.viewControllers[0] animated:YES];
//    [self.navigationController popToRootViewControllerAnimated:YES];
}
@end
```
