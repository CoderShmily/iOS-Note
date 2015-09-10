# UIGestureRecognizer


利用UIGestureRecognizer，能轻松识别用户在某个view上面做的一些常见手势

1. UIGestureRecognizer是一个抽象类，定义了所有手势的基本行为，使用它的子类才能处理具体的手势
    - UITapGestureRecognizer(敲击)
    - UIPinchGestureRecognizer(捏合，用于缩放)
    - UIPanGestureRecognizer(拖拽)
    - UISwipeGestureRecognizer(轻扫)
    - UIRotationGestureRecognizer(旋转)
    - UILongPressGestureRecognizer(长按)
    

###设置手势识别器对象的具体属性
```objc
// 连续敲击2次
tap.numberOfTapsRequired = 2;
// 需要2根手指一起敲击
tap.numberOfTouchesRequired = 2;

添加手势识别器到对应的view上
[self.iconView addGestureRecognizer:tap];

监听手势的触发
[tap addTarget:self action:@selector(tapIconView:)];
```
###手势识别的状态
```objc
typedef NS_ENUM(NSInteger, UIGestureRecognizerState) {
    // 没有触摸事件发生，所有手势识别的默认状态
    UIGestureRecognizerStatePossible,
    // 一个手势已经开始但尚未改变或者完成时
    UIGestureRecognizerStateBegan,
    // 手势状态改变
    UIGestureRecognizerStateChanged,
    // 手势完成
    UIGestureRecognizerStateEnded,
    // 手势取消，恢复至Possible状态
    UIGestureRecognizerStateCancelled,
    // 手势失败，恢复至Possible状态
    UIGestureRecognizerStateFailed,
    // 识别到手势识别
    UIGestureRecognizerStateRecognized = UIGestureRecognizerStateEnded
};

```


##测试范例

```objc
@interface ViewController ()<UIGestureRecognizerDelegate>

@property (weak, nonatomic) IBOutlet UIImageView *imageView;

@end

@implementation ViewController

- (void)viewDidLoad {

     [super viewDidLoad];

    UIPinchGestureRecognizer *pinch = [[UIPinchGestureRecognizer alloc] initWithTarget:self action:@selector(pinch:)];
    [_imageView addGestureRecognizer:pinch];


    UIPanGestureRecognizer *pan = [[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(pan:)];
    [_imageView addGestureRecognizer:pan];


    UIRotationGestureRecognizer *rotate = [[UIRotationGestureRecognizer alloc] initWithTarget:self action:@selector(rotate:)];
    [_imageView addGestureRecognizer:rotate];

    // 设置三者代理，实现gestureRecognizer代理方法，
    // 同时实现多个手势操作
    pinch.delegate = self;
    pan.delegate = self;
    rotate.delegate = self;
}

- (void)pinch:(UIPinchGestureRecognizer *)pinch
{
    CGFloat scale = pinch.scale;

    _imageView.transform = CGAffineTransformScale(_imageView.transform, scale, scale);

    pinch.scale = 1;

}

- (void)pan:(UIPanGestureRecognizer *)pan
{
    CGPoint point = [pan translationInView:_imageView];

    _imageView.transform = CGAffineTransformTranslate(_imageView.transform, point.x, point.y);

    [pan setTranslation:CGPointZero inView:_imageView];
}

- (void)rotate:(UIRotationGestureRecognizer *)rotate
{
    CGFloat r = rotate.rotation;

    _imageView.transform = CGAffineTransformRotate(_imageView.transform, r);

    rotate.rotation = 0;
}

- (BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldRecognizeSimultaneouslyWithGestureRecognizer:(UIGestureRecognizer *)otherGestureRecognizer
{
    return YES;
}

- (void)setUpSwipe
{
    // 轻扫
    // 轻扫默认的方向：向右
    // 一个轻扫手势只能支持一个方向
    // 一个控件可以添加很多手势
    UISwipeGestureRecognizer *swipeLeft = [[UISwipeGestureRecognizer alloc] initWithTarget:self action:@selector(swipe:)];

    swipeLeft.direction = UISwipeGestureRecognizerDirectionLeft;

    [_imageView addGestureRecognizer:swipeLeft];

    UISwipeGestureRecognizer *swipeRight = [[UISwipeGestureRecognizer alloc] initWithTarget:self action:@selector(swipe:)];

    swipeRight.direction = UISwipeGestureRecognizerDirectionRight;


    [_imageView addGestureRecognizer:swipeRight];
}

- (void)swipe:(UISwipeGestureRecognizer *)swipe
{
    if (swipe.direction == UISwipeGestureRecognizerDirectionLeft) {
        NSLog(@"%s",__func__);
    }
}

#pragma mark - 长按
- (void)setUpLongPress
{
    // 长按
    UILongPressGestureRecognizer *longPres = [[UILongPressGestureRecognizer alloc] initWithTarget:self action:@selector(longPress:)];

    [_imageView addGestureRecognizer:longPres];
}

// 这个方法调用频率跟随手指的变化,最少调用2次
- (void)longPress:(UILongPressGestureRecognizer *)longPress
{

    if (longPress.state == UIGestureRecognizerStateBegan) {
        NSLog(@"%ld",longPress.state);
    }
}
#pragma mark - 点按
- (void)setUpTap
{
    // 创建点按手势
    // 当触发点按手势的时候就会调用Target对象action方法
    UITapGestureRecognizer *tap = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(tap)];

//        tap.delegate = self;

    // 点按
    [_imageView addGestureRecognizer:tap];
}

 // 是否允许接收一个手指
- (BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldReceiveTouch:(UITouch *)touch
{

     // 获取当前触摸点
    CGPoint curP = [touch locationInView:_imageView];

    // 如果在图片的左边，不允许接收这个手指
    if (curP.x < _imageView.bounds.size.width * 0.5) {
        // 左边
        return NO;
    }else{
        return YES;
    }
}


- (void)tap
{
    NSLog(@"%s",__func__);
}


@end
```

1.按住option出现手势 可以缩小两指之间的距离

2.同时按住shift 移动鼠标可以在保持两指距离的情况下，移动

3.移动到控件后，松开shift，保持单击鼠标拖拽两指间的距离

