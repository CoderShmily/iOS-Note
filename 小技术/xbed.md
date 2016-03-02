# Xbed

```objc
// 带有navigationController的从导航栏下面开始布局
self.navigationController.navigationBar.translucent = NO;


self.automaticallyAdjustsScrollViewInsets = NO;

self.edgesForExtendedLayout = UIRectEdgeNone;

// 手动布局要用 masonry默认设置了
self.translatesAutoresizingMaskIntoConstraints = NO;
```

```
