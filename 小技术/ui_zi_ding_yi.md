# UI 自定义

- UITabBar

```objc
1.使用继承UITabBar的自定义类，在UITabBarController中
    [self setValue:[[MYTabBar alloc] init] forKeyPath:@"tabBar"];
2.修改UITabBar的Frame
self.tabBar.frame = CGRectMake(0,0,0,0);
3.
```
    self.tabBar.width = 100;
    NSLog(@"%@", NSStringFromCGRect(self.tabBar.frame));
    self.tabBar.y -= 50;
    ```