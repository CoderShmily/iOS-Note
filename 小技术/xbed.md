# Xbed

```objc
#define IS_IPHONE_3_5Inche (IS_IPHONE && [UIScreen mainScreen].bounds.size.width == 320 &&[UIScreen mainScreen].bounds.size.height == 480)
#define IS_IPHONE_4_0Inche (IS_IPHONE && [UIScreen mainScreen].bounds.size.width == 320 &&[UIScreen mainScreen].bounds.size.height == 568)
#define IS_IPHONE_4_7Inche (IS_IPHONE && [UIScreen mainScreen].bounds.size.width == 375 &&[UIScreen mainScreen].bounds.size.height == 667)
#define IS_IPHONE_5_5Inche (IS_IPHONE && [UIScreen mainScreen].bounds.size.width == 414 &&[UIScreen mainScreen].bounds.size.height == 736)
```
```objc
// 带有navigationController的从导航栏下面开始布局
self.navigationController.navigationBar.translucent = NO;

```

