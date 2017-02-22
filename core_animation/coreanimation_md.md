# Core Animation

### CAReplicatorLayer + CABasicAnimation

`复制层 + 层动画`

```objc
- (void)viewDidLoad {
    [super viewDidLoad];

    CAReplicatorLayer *co = [CAReplicatorLayer layer];

    CALayer *layer = [CALayer layer];

    CABasicAnimation *basic = [CABasicAnimation animation];

    co.frame = CGRectMake(0, 0, 300, 300);
    co.backgroundColor = [UIColor greenColor].CGColor;
    co.instanceCount = 3;
    co.instanceTransform = CATransform3DMakeTranslation(35, 0, 0);
    co.instanceDelay = 0.5;
    co.instanceColor = [UIColor redColor].CGColor;
    co.instanceRedOffset -= 0.3;
    co.instanceGreenOffset -= 0.3;
    co.instanceBlueOffset -= 0.3;

    layer.bounds = CGRectMake(0, 0, 30, 100);
    layer.backgroundColor = [UIColor whiteColor].CGColor;
    layer.anchorPoint = CGPointMake(0, 1);
    layer.position = CGPointMake(0, co.bounds.size.height);

    basic.keyPath = @"transform.scale.y";
    basic.toValue = @0;
    basic.duration = 1;
    basic.autoreverses = YES;
    basic.repeatCount = MAXFLOAT;
//    basic.repeatDuration = 3; // 动画执行次数，有设置autoreverses 一个完整的动画次数加1

    [layer addAnimation:basic forKey:nil];

    [co addSublayer:layer];

    [self.view.layer addSublayer:co];

}
```



