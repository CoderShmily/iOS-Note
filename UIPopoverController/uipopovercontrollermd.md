### UIPopoverController

```
设置内容控制器
- (id)initWithContentViewController:(UIViewController *)viewController;
设置内容给的尺寸
@property (nonatomic) CGSize popoverContentSize;
设置显示的位置
- (void)presentPopoverFromBarButtonItem:(UIBarButtonItem *)item permittedArrowDirections:(UIPopoverArrowDirection)arrowDirections animated:(BOOL)animated;
- (void)presentPopoverFromRect:(CGRect)rect inView:(UIView *)view permittedArrowDirections:(UIPopoverArrowDirection)arrowDirections animated:(BOOL)animated;
在销毁popover对象之前，一定先让popover消失（不可见）
- (void)dismissPopoverAnimated:(BOOL)animated;
通过内容控制器设置内容尺寸常用属性
@property (nonatomic) CGSize preferredContentSize;
常用属性
代理对象 @property (nonatomic, assign) id <UIPopoverControllerDelegate> delegate;
是否可见 @property (nonatomic, readonly, getter=isPopoverVisible) BOOL popoverVisible;
箭头方向 @property (nonatomic, readonly) UIPopoverArrowDirection popoverArrowDirection;


默认情况下
只要UIPopoverController显示在屏幕上，UIPopoverController背后的所有控件默认是不能跟用户进行正常交互的
点击UIPopoverController区域外的控件，UIPopoverController默认会消失
要想点击UIPopoverController区域外的控件时不让UIPopoverController消失，解决办法是设置passthroughViews属性
@property (nonatomic, copy) NSArray *passthroughViews;

这个属性是设置当UIPopoverController显示出来时，哪些控件可以继续跟用户进行正常交互。这样的话，点击区域外的控件就不会让UIPopoverController消失了
```