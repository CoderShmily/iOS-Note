##一、IOS启动过程

![](./ios-start.png)
- 程序启动过程

main -》 UIApplicationMain -》 通知代理做事情


UIApplicationMain底层实现:
```objc
// principalClassName:描述UIApplication的类名字符串
// delegateClassName:描述UIApplication代理的类名字符串

int UIApplicationMain(int argc, char *argv[], NSString *principalClassName, NSString *delegateClassName);
```
1. 创建UIApplication对象
2. 创建UIApplication的代理对象，并且设置为UIApplication的代理.
3. 开启一个主运行循环，处理事件
4. 加载info.plist文件，判断是否有Main.storyboard,如果有就会去加载。
有Main.storyboard才会执行第5步
5. 加载Main.storyboard，
    - 创建窗口
    - 加载Main.storyboard，初始化storyboard描述的控制器
    - 设置窗口的根控制器，并且显示窗口

#### 1. UIApplicationMain
![](./UIApplicationMain.png)
![](./UIApplicationMain2.png)





二、窗口，应用程序创建的第一个控件。
1.添加子控件，一般把窗口的根控制器添加上去，通过设置rootViewController就可以把根控制器的view添加到窗口上。
2.介绍窗口如何显示。
2.1 设置窗口的hiddle属性
3.应用程序是有主窗口，通常调用makeKeyAndVisible
4.windons属性，可以识别应用程序中哪些控件还是窗口，有一个比较特殊，状态栏。
5.window层级，alert(2000) > statusBar(1000) > normal(0)

#### 2. UIWindow
-  UIWindow是一种特殊的UIView，通常在一个app中只会有一个UIWindow

- iOS程序启动完毕后，创建的第一个视图控件就是UIWindow，接着创建控制器的view，最后将控制器的view添加到UIWindow上，于是控制器的view就显示在屏幕上了

- 一个iOS程序之所以能显示到屏幕上，完全是因为它有UIWindow
    也就说，没有UIWindow，就看不见任何UI界面
![](./UIWindow1.png)
1.UIWindow的获得
![](./UIWindow2.png)

```objc

// 1.创建窗口
// 2.加载Main.storyboard，并且创建storyboard描述的控制器
// 3.把storyboard描述的控制器作为窗口的根控制器
// 4.显示窗口

//注意：窗口要显示出来必须的步骤 1.强引用窗口 2.设置窗口的尺寸
// 在程序启动完成的时候，仅仅只是帮你创建好窗口，并不会马上显示。

// @implementation AppDelegate 中

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

//    NSLog(@"%@",self.window);
    NSLog(@"%@",application.keyWindow);
    [self windowLevel];
    return YES;
}
- (void)windowLevel // 自定义
{

    self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];
    //    self.window.alpha = 0.6;
    // 设置窗口的层级 UIWindowLevelAlert UIWindowLevelNormal UIWindowLevelStatusBar
    // UIWindowLevelAlert > UIWindowLevelStatusBar > UIWindowLevelNormal
    //    self.window.windowLevel = UIWindowLevelStatusBar + 1;

    NSLog(@"%f",UIWindowLevelAlert);
    self.window.backgroundColor = [UIColor yellowColor];

    // 状态栏是一个窗口，键盘也是一个窗口
    // application.windows:可以利用这个属性，学习下当前API哪些控件是窗口


    // 弹出键盘,UITextField就是用来弹出键盘，不需要展示
    // 注意，如果想通过textField弹出键盘，必须要把textField添加到某个控件上
    //    UITextField *textField = [[UITextField alloc] init];
    //    [textField becomeFirstResponder];
    //    [self.window addSubview:textField];

    // 让窗口显示,让窗口成为我们应用程序的主窗口
    [self.window makeKeyAndVisible];
    // 1.让窗口成为应用程序的主窗口 2.设置窗口不要给隐藏
    //    1. application.keyWindow = self.window
    //    2. self.window.hidden = NO;

    // 创建第二个窗口
    self.window1 = [[UIWindow alloc] initWithFrame:CGRectMake(50, 400, 300, 300)];

    self.window1.backgroundColor = [UIColor redColor];

    self.window1.windowLevel = UIWindowLevelStatusBar;
    NSLog(@"%f",self.window1.windowLevel);

    self.window1.hidden = NO;

    //    NSLog(@"%@",application.windows);

}

- (void)setWindow  //自定义
{
    // 1.创建窗口
    self.window = [[UIWindow alloc] initWithFrame:[UIScreen mainScreen].bounds];

    self.window.backgroundColor = [UIColor redColor];

    // 2.创建窗口的根控制器
    UIViewController *rootVc = [[UIViewController alloc] init];
    rootVc.view.backgroundColor = [UIColor greenColor];

    //    UIButton *addBtn = [UIButton buttonWithType:UIButtonTypeContactAdd];
    //    addBtn.center = CGPointMake(200, 200);
    //    [rootVc.view addSubview:addBtn];
    //    [self.window addSubview:rootVc.view];

    // 把控制器作为窗口的跟控制器，就会把跟控制器的view自动添加到窗口
    // 设置了窗口的根控制器，有旋转的功能
    self.window.rootViewController = rootVc;

    // 3.显示窗口
    [self.window makeKeyAndVisible];

}
```
#### 3.四大对象关系图
![](./relation.png)

