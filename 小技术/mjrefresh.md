# MJRefresh
```objc

1.下拉刷新原理：
大部分的下拉刷新控件，都是用contentInset实现的。默认情况下，如果一个UIScrollView的左上角在导航栏的正下方，那么它的contentInset是64，而contentOffset是-64。继续下拉的话，contentOffset就会越来越小，如果上滑，contentOffset就会增大，直到左上角达到屏幕的左上角时，contentOffset刚好为0
默认情况下，如果下拉一个UIScrollView，在松手之后，会弹回初始的位置（导航栏下方）。
而大部分的下拉刷新控件，都是将自己放在UIScrollView的上方，起始y设置成负数，所以平时不会显示出来，只有下拉的时候才会出现，放开又会弹回去。然后在loading的时候，临时把contentInset增大，相当于把UIScrollView往下挤，于是下拉刷新的控件就会显示出来，然后刷新完成之后，再把contentInset改回原来的值，实现回弹的效果

view加在x=0 ,w=superW ,y=-h ,h=MJRefreshHeaderHeight(54);

2.跳转到下一个控制器时，contentInset可能会变 记得考虑

3.用了UIScrollView+MJRefresh里的一个category，为UIScrollView增加了属性header和footer。
这里用到了关联对象的技巧（AssociatedObject），因为category通常情况下是不能直接添加实例变量的.

4.设置了alwaysBounceVertical，这样才能确保UIScrollView可以下拉，否则需要处理contentSize才能拉得动，就麻烦了很多
```
```objc
1.NSUserDefaults
// NSUserDefaults 用时记得 synchronize
[[NSUserDefaults standardUserDefaults] setObject:nil forKey:nil];
[[NSUserDefaults standardUserDefaults] synchronize];

2.sectionheader停留解决
//（部分）
CGFloat insetT = - self.scrollView.mj_offsetY > _scrollViewOriginalInset.top ? - self.scrollView.mj_offsetY : _scrollViewOriginalInset.top;
insetT = insetT > self.mj_h + _scrollViewOriginalInset.top ? self.mj_h + _scrollViewOriginalInset.top : insetT;
self.scrollView.mj_insetT = insetT;

// 摘自网络
- (void)scrollViewDidScroll:(UIScrollView *)scrollView {
    CGFloat sectionHeaderHeight = 50;
    if (scrollView.contentOffset.y<=sectionHeaderHeight&&scrollView.contentOffset.y>=0) {
        scrollView.contentInset = UIEdgeInsetsMake(-scrollView.contentOffset.y, 0, 0, 0);
    } else if (scrollView.contentOffset.y>=sectionHeaderHeight) {
        scrollView.contentInset = UIEdgeInsetsMake(-sectionHeaderHeight, 0, 0, 0);
    }
}
        
```