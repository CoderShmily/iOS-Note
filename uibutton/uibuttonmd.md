# UIButton

##### UIButton的创建
```objc
UIButton *btn = [UIButton buttonWithType: UIButtonTypeCustom];
[btn setImage:[UIImage imageNamed:@"hello"] forState:(UIControlState *)];
```
- UIButton的状态

    - normal（普通状态）
    默认情况（Default）
    对应的枚举常量：UIControlStateNormal

    - highlighted（高亮状态）
    按钮被按下去的时候（手指还未松开）
    对应的枚举常量：UIControlStateHighlighted

    - disabled（失效状态，不可用状态）
    如果enabled属性为NO，就是处于disable状态，代表按钮不可以被点击
    对应的枚举常量：UIControlStateDisabled


- UIControlState的可选枚举类型
```objc
UIControlStateNormal
UIControlStateHighlighted
UIControlStateDisabled
UIControlStateSelected
UIControlStateApplication
UIControlStateReserved
```
###UIButton的常用设置方法

```objc
// 设置按钮的文字
- (void)setTitle:(NSString *)title forState:(UIControlState)state;
// 设置按钮的文字颜色
- (void)setTitleColor:(UIColor *)color forState:(UIControlState)state;
// 设置按钮内部的小图片
- (void)setImage:(UIImage *)image forState:(UIControlState)state;
// 设置按钮的背景图片
- (void)setBackgroundImage:(UIImage *)image forState:(UIControlState)state;
// 设置按钮的文字字体（需要拿到按钮内部的label来设置）
btn.titleLabel.font = [UIFont systemFontOfSize:13];
// 获得按钮的文字
- (NSString *)titleForState:(UIControlState)state;
// 获得按钮的文字颜色
- (UIColor *)titleColorForState:(UIControlState)state;
// 获得按钮内部的小图片
- (UIImage *)imageForState:(UIControlState)state;
// 获得按钮的背景图片
- (UIImage *)backgroundImageForState:(UIControlState)state;
```

### 自定义按钮：调整内部子控件的frame
1. 由于在外部设置完按钮的子控件frame后，还会被恢复默认设置。所以在以下两种方法中设置。
2. layoutSubviews中调用[superlayoutSubviews]方法后，然后在这里对子控件布局，就可以控制子控件的大小。

    - 方式1：实现titleRectForContentRect:和imageRectForContentRect:方  法，分别返回titleLabel和imageView的frame(参数contentRect.size 一般情况下等于 self.bounds.size)
    - 方式2`(建议)`：在layoutSubviews方法中设置

### 自定义按钮：`按钮的子控件的赋值`
1. 按钮的子控件不能直接赋值，不然无法显示出来，只能通过按钮的set方法在设置状态的同时设置子控件
2. 按钮的属性能不能直接修改取决于属性是否分状态
    - -(void)setTitle:(NSString *)title forState: (UIControlState) state;
    - -(void)setImage:(UIImage *)image forState:(UIControlState) state;

### 自定义按钮：调整内边距
```objc
// 设置按钮内容的内边距（影响到imageView和titleLabel）
@property(nonatomic) UIEdgeInsets contentEdgeInsets;
// 设置titleLabel的内边距（影响到titleLabel）
@property(nonatomic) UIEdgeInsets titleEdgeInsets;
// 设置imageView的内边距（影响到imageView）
@property(nonatomic) UIEdgeInsets imageEdgeInsets;
```

