# UI 自定义

- ###UITabBar

```objc
# 1.使用继承UITabBar的自定义类，在UITabBarController中
[self setValue:[[MYTabBar alloc] init] forKeyPath:@"tabBar"];

# 2.UITabBarController中修改UITabBar的Frame（此方法不能修改高度）
self.tabBar.frame = CGRectMake(0,0,0,0);

# 3.修改UITabBar的高度
- (void)viewWillLayoutSubviews{
    CGRect tabFrame = self.tabBar.frame;
    // W=300 H=35
    tabFrame.size.height = 35;
    tabFrame.size.width = 300;
    // 距离屏幕底部20
    tabFrame.origin.y = self.view.height - tabFrame.size.height - 20;
    self.tabBar.frame = tabFrame;
    self.tabBar.centerX = self.view.centerX;
}
// 如果想要添加的工具条点击事件生效请在viewWillLayoutSubviews添加此代码（没啥问题，没试过）
[self.tabBar bringSubviewToFront:self.bottomToolView];

# 4.UITabBar布局子控件
- (void)layoutSubviews
{
    [super layoutSubviews];
    // 处理其他按钮
    CGFloat buttonW = self.width / 5;
    CGFloat i = 0;
    for (UIView *tabBarButton in self.subviews) {
        if (![NSStringFromClass(tabBarButton.class) isEqualToString:@"UITabBarButton"]) continue;
        
        tabBarButton.width = buttonW;
        tabBarButton.x = i * buttonW;
        if (i > 1) tabBarButton.x += buttonW;
        i++;
    }
}
```


- ###UITabBarItem
```objc
# 1. 只有图片时，位置显示不正确，调整
vc.tabBarItem.imageInsets = UIEdgeInsetsMake(8, 0, -8, 0);
```
- ###UItableView
```objc
# 1.去掉多余的cell
tableView.tableFooterView = [[UIView alloc] init];
```
- ###UISearchBar

```objc
UISearchBar *searchBar = [[UISearchBar alloc] init];

# 1.设置searchBar背景色
// (1)backgroundColor 被遮住了，不行
searchBar.backgroundColor = [UIColor redColor];
// (2)
searchBar.barTintColor = [UIColor redColor];
// (3)如果barTintColor设置后底部有一条线，可以用背景图片
searchBar.backgroundImage = [UIImage imageNamed:@""];

// 去掉背景色只能
searchBar.backgroundImage = [UIImage imageWithColor:[UIColor clearColor]];

# 2.设置放大镜图标
[searchBar setImage:[UIImage imageNamed:@""] forSearchBarIcon:UISearchBarIconSearch state:UIControlStateNormal];

#3. 设置输入框（默认距离searchBar左右间距10）
// 设置背景(可以改输入框高度) 给个确定高度的图片可以改输入框高度，图片宽度要大于1
[searchBar setSearchFieldBackgroundImage:[UIImage imageNamed:@""] forState:UIControlStateNormal];

// 拿到搜索框内部的UITextField
UITextField *searchField = [searchBar valueForKey:@"_searchField"];
//    searchField.backgroundColor = [UIColor whiteColor];
//    searchField.borderStyle = UITextBorderStyleRoundedRect; // 设置圆角 背景图片什么的失效
//    searchField.layer.cornerRadius = 10;
//    searchField.layer.masksToBounds = YES;

// 修改输入文字颜色
searchField.textColor = COLOR_WITH_RGB(172, 150, 240);
    
// 修改占位文字的颜色
[searchField setValue:COLOR_WITH_RGB(172, 150, 240) forKeyPath:@"_placeholderLabel.textColor"];

# 4.UISearchDisplayController
// searchBar都是指自己创建的，不是UISearchDisplayController的searchBar(只读)
// 带有导航栏的，搜索控制器出现后向上移问题
@implementation CXSearchDisplayController
- (void)setActive:(BOOL)visible animated:(BOOL)animated
{
    [super setActive: visible animated: NO]; // 为了去掉切换的时候 导航栏闪过的黑色背景
    // 禁止隐藏导航栏
    [self.searchContentsController.navigationController setNavigationBarHidden: NO animated: NO];
}
@end

// 搜索控制器出来的时候，searchBar样式背景会变深灰色，如果searchBar单独布局在superView中背景色与superView一致是看不出来的）
// 这样输入框和搜索框同样大小，可以同时改searchBar的高度和setSearchFieldBackgroundImage的高度
searchBar.searchBarStyle = UISearchBarStyleMinimal;

---------------------------以下未测试------------------------------------------------
    UITextField *searchField;
    if ([[[UIDevice currentDevice] systemVersion] floatValue]<7.0)
        searchField=[searchBar.subviews objectAtIndex:1];
    else
        searchField=[((UIView *)[searchBar.subviews objectAtIndex:0]).subviews lastObject];
    
    5、修改UISearchBar右侧的取消按钮文字颜色及背景图片
#pragma mark 搜索框的代理方法，搜索输入框获得焦点（聚焦）
    -(void)searchBarTextDidBeginEditing:(UISearchBar *)searchBar{	[searchBar setShowsCancelButton:YES animated:YES];
        // 修改UISearchBar右侧的取消按钮文字颜色及背景图片
        for (UIView *searchbuttons in [searchBar subviews]){
            if ([searchbuttons isKindOfClass:[UIButton class]]) {
                UIButton *cancelButton = (UIButton*)searchbuttons;
                // 修改文字颜色
                [cancelButton setTitleColor:[UIColor whiteColor] forState:UIControlStateNormal];
                [cancelButton setTitleColor:[UIColor whiteColor] forState:UIControlStateHighlighted];
                // 修改按钮背景
                [cancelButton setBackgroundImage:[UIImage resizedImage:@"login_btn_login.png"] forState:UIControlStateNormal];
                [cancelButton setBackgroundImage:nil forState:UIControlStateHighlighted];
            }
        }
    }
    注：修改取消按钮文字颜色及背景图片的代码片段，一定要放到取消按钮会显示代理方法中修改，否则遍历找不着呀，那就修改不了了。
```