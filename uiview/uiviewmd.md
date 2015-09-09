## UIVIew

#### UIView的常见属性
- @property(nonatomic,readonly) UIView *superview;  // 获得自己的父控件对象
- @property(nonatomic,readonly,copy) NSArray *subviews; // 获得自己的所有子控件对象
- @property(nonatomic) NSInteger tag; // 控件的ID(标识)，父控件可以通过tag来找到对应的子控件
- @property(nonatomic) CGAffineTransform transform;// 控件的形变属性(可以设置旋转角度、比例缩放、平移等属性)
- @property(nonatomic) CGRect frame; // 控件矩形框在父控件中的位置和尺寸(以父控件的左上角为坐标原点)
- @property(nonatomic) CGRect bounds; // 控件矩形框的位置和尺寸(以自己左上角为坐标原点，所以bounds的x、y一般为0)
- @property(nonatomic) CGPoint center; // 控件中点的位置(以父控件的左上角为坐标原点)
- @property(nonatomic,readonly) CGSize  size; // 只读，获得图片的尺寸

- @property(nonatomic) UIViewContentMode contentMode; // 内容模式：用来控制图片如何显示
- @property(nonatomic) BOOL clipsToBounds; // 超出图片的部分是否裁剪


#### UIView的常见方法
```objc
- (void)addSubview:(UIView *)view; // 添加一个子控件view
- (void)removeFromSuperview; // 从父控件中移除
- (UIView *)viewWithTag:(NSInteger)tag; // 根据一个tag标识找出对应的控件（一般都是子控件）
```

####范例
```objc
UIView *tempView = [[UIView alloc] init];
tempView.backgroundColor = [UIColor redColor];
tempView.frame = CGRectMake(100, 100, 100, 100); // 修改位置和尺寸
tempView.bounds = CGRectMake(0, 0, 100, 100); // 修改尺寸
tempView.center = CGPointMake(100, 100); // 修改位置
[self.view addSubview:tempView];

UISwitch *s = [[UISwitch alloc] init];
s.center = CGPointMake(100, 100);

UIView *purpleView = [self.view viewWithTag:10];

[self.purpleView addSubview:s];
```
