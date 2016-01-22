# UI 自定义

- UITabBar

```objc
// 1.使用继承UITabBar的自定义类，在UITabBarController中
[self setValue:[[MYTabBar alloc] init] forKeyPath:@"tabBar"];

// 2.修改UITabBar的Frame
// 此方法不能修改高度
self.tabBar.frame = CGRectMake(0,0,0,0);

// 3.修改UITabBar的高度
- (void)viewWillLayoutSubviews{
    CGRect tabFrame = self.tabBar.frame; //self.TabBar is IBOutlet of your TabBar
    tabFrame.size.height = 80;
    tabFrame.origin.y = self.view.frame.size.height - 80;
    self.tabBar.frame = tabFrame;
}
// 如果想要添加的工具条点击事件生效请在viewWillLayoutSubviews添加此代码
[self.tabBar bringSubviewToFront:self.bottomToolView];


// 4.UITabBar布局子控件
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

