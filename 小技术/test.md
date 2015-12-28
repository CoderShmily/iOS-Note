
#### QQ的侧滑 思路
```
主界面是UITabBarController
侧滑： 
1.每个UITabBarItem对应的View添加UIPanGestureRecognizer手势
view(VC)若一致就写基类baseView，不一致就写共同基类的分类 处理手势操作
2.左侧的view初始化为UITabBarController的View
3.右侧view移动的距离 按比例移动左侧view
```

```objc
!block ? : block();
```
```objc
// 获取父视图
self.superview
```
```objc
Masonry小技巧
1.获取真实frame
-(void) layoutSubviews
{
    [super layoutSubviews];
    // code
}

2.实现动画效果
[switchBar mas_remakeConstraints:^(MASConstraintMaker *make) {
    make.right.equalTo(@0);
    make.bottom.equalTo(@0);
    make.width.equalTo(toupiaoBtn);
    make.height.equalTo(@2);
}];

[UIView animateWithDuration:0.3 delay:0.0 options:UIViewAnimationOptionCurveLinear animations:^{
    [switchBar layoutIfNeeded];
} completion:nil];
```