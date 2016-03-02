# Xbed

```objc
// 带有navigationController的从导航栏下面开始布局
self.navigationController.navigationBar.translucent = NO;


self.automaticallyAdjustsScrollViewInsets = NO;

self.edgesForExtendedLayout = UIRectEdgeNone;

// 手动布局要用 masonry默认设置了
self.translatesAutoresizingMaskIntoConstraints = NO;
```
edit断点设置 条件

设置观察点

watchpoint set variable self->_test->_var   注意写法，一定要用->而且，要用下划线取得变量。不能用" . "。
再配合条件断点，在某些情况下，调试起来非常有效：下面demo显示了：“在_var == 2的时候才断点”的用法
watchpoint set variable

```
