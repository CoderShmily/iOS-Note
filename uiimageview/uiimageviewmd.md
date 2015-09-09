# UIImageView


### UIImageView的常见属性
```objc
@property(nonatomic,retain) UIImage *image; // 显示的图片
// 显示的动画图片
@property(nonatomic,copy) NSArray *animationImages;
// 动画图片的持续时间
@property(nonatomic) NSTimeInterval animationDuration;
// 动画的播放次数（默认是0，代表无限播放）
@property(nonatomic) NSInteger animationRepeatCount;

```
### UIImageView继承自UIView的重要属性
 - **contentMode  内容模式：一般用来控制图片如何显示**

```objc
#1 有scale的有拉伸
    #1.1 只有aspect的
        UIViewContentModeScaleToFill, // 将图片拉伸至填充整个imageView,图片显示的尺寸跟imageView的尺寸是一样的
    #1.2 有scale的，且有aspect单词的：保持图片原来的宽高比
        UIViewContentModeScaleAspectFit,      // contents scaled to fit with fixed aspect. remainder is transparent // 保证刚好能看到图片的全部
        UIViewContentModeScaleAspectFill,     // contents scaled to fill with fixed aspect. some portion of content may be clipped.拉伸至图片的宽度或者高度跟imageView一样
#2 没有scale的：图片绝对不会被拉伸，保持图片的原尺寸
        UIViewContentModeRedraw, // redraw on bounds change (calls -setNeedsDisplay)
        UIViewContentModeCenter, // contents remain same size. positioned adjusted.
        UIViewContentModeTop,
        UIViewContentModeBottom,
        UIViewContentModeLeft,
        UIViewContentModeRight,
        UIViewContentModeTopLeft,
        UIViewContentModeTopRight,
        UIViewContentModeBottomLeft,
        UIViewContentModeBottomRight
```
### UIImageView的创建方法
```objc
UIImageView *imageView = [[UIImageView alloc] init];
UIImageView *imageView = [[UIImageView alloc] initWithFrame:(UIImage *)];

// 利用这个方法创建出来的imageView的尺寸和传入的图片尺寸一样
UIImageView *imageView = [[UIImageView alloc] initWithImage:(UIImage *)];

```
### 小语法点 修改frame的3种方式
- 不能直接修改：OC对象的结构体属性的成员
- 下面的写法是错误的

```objc
imageView.frame.size = imageView.image.size;
```
- **1.直接使用CGRectMake函数**

```objc
imageView.frame = CGRectMake(100, 100, 200, 200);
```
- **2.利用临时结构体变量**

```objc
CGRect tempFrame = imageView.frame;
tempFrame.origin.x = 100;
tempFrame.origin.y = 100;
// tempFrame.size = imageView.image.size;
tempFrame.size.width = 200;
tempFrame.size.height = 200;
imageView.frame = tempFrame;
```
- **3.使用大括号{}形式**

```objc
imageView.frame = (CGRect){{100, 100}, {200, 200}};
```
### 延迟做一些事情

```objc
[abc performSelector:@selector(stand:) withObject:@"123" afterDelay:10];
// 10s后自动调用abc的stand:方法，并且传递@"123"参数
```
##`帧动画`

`UIImageView的动画常见方法`

```objc
- (void)startAnimating; // 开始动画
- (void)stopAnimating; // 停止动画
- (BOOL)isAnimating; // 是否正在执行动画
```
- 放在Images.xcassets 的图片只能用imageNamed(会被压缩，路径不对)
- 放在Supporting Files 的图片能用imageNamed 或者 imageWithContentsOfFile 方法

```objc
@property (weak, nonatomic) IBOutlet UIImageView *imageView;

// 加载所有的动画图片
    NSMutableArray *images = [NSMutableArray array];
    for (int i = 1; i<=10; i++)
    {
// 方法1  文件不用加扩展名
    UIImage *image = [UIImage imageNamed:[NSString stringWithFormat:@"stand_%d", i]];

// 方法2 需要在文件名或者类型中加入扩展名
//  NSString *filename = [NSString stringWithFormat:@"%@_%d", filenamePrefix, i];
//  NSString *file = [[NSBundle mainBundle] pathForResource:filename ofType:@"png"];
//  UIImage *image = [UIImage imageWithContentsOfFile:file];

    [images addObject:image];
    }

    // 设置动画图片
    self.imageView.animationImages = images;
    // 设置播放次数
    self.imageView.animationRepeatCount = 1;
    // 设置图片
    self.imageView.image = [UIImage imageNamed:@"stand_1"];
    // 开始动画
    [self.imageView startAnimating];

```

##`声音播放`
```objc
#import <AVFoundation/AVFoundation.h>

#//@property (strong, nonatomic) AVPlayer *player;

// 创建一个音频文件的URL(URL就是文件路径对象)
NSURL *url = [[NSBundle mainBundle] URLForResource:@"dazhao" withExtension:@"mp3"];
// 播放声音
self.player = [AVPlayer playerWithURL:url];
// 播放
[self.player play];
```

###范例
```objc
// 初始化
UIImageView *imageView = [[UIImageView alloc] init];
imageView.image = [UIImage imageNamed:@"meinv"];
UIImageView *imageView = [[UIImageView alloc] initWithImage:[UIImage imageNamed:@"meinv"]];

UIImageView *imageView = [[UIImageView alloc] init];
imageView.image = [UIImage imageNamed:@"meinv"];
imageView.frame = CGRectMake(100, 100, imageView.image.size.width, imageView.image.size.height);

// frame结构体赋值 3种方法
CGRect tempFrame = imageView.frame;
tempFrame.size.width = imageView.image.size.width;
tempFrame.size.height = imageView.image.size.height;
imageView.frame = tempFrame;

CGRect tempFrame = imageView.frame;
tempFrame.size = imageView.image.size;
tempFrame.origin.y = 100;
imageView.frame = tempFrame;

imageView.frame = (CGRect){CGPointZero, imageView.image.size};

// 内容模式：一般用来控制图片如何显示
imageView.contentMode = UIViewContentModeScaleAspectFit;

// 裁剪超出imageView边框的部分
imageView.clipsToBounds = YES;

[self.view addSubview:imageView];
```

###九宫格代码

```objc
@interface ViewController ()
@property (weak, nonatomic) IBOutlet UIView *shopsView;

@end

@implementation ViewController
- (IBAction)add
{
    NSInteger Col = 3;
    NSInteger Row = 4;

    CGFloat ShopW = 70;
    CGFloat ShopH = 90;

    NSInteger index = self.shopsView.subviews.count;

    CGFloat MarginX = (self.shopsView.frame.size.width - ShopW * Col) / (Col -1);
    CGFloat SHopX = (index % Col) * (MarginX + ShopW);

    CGFloat MarginY = (self.shopsView.frame.size.height - ShopH * Row) / (Row - 1);
    CGFloat ShopY = (index / Col) * (MarginY + ShopH);
    if (index / Col > Row - 1) {
        return;
    }

    UIView *shopView = [[UIView alloc] initWithFrame:CGRectMake(SHopX, ShopY, ShopW, ShopH)];
    [self.shopsView addSubview:shopView];

    UIImageView *image = [[UIImageView alloc] initWithImage:[UIImage imageNamed:@"图片名"]];
    image.frame = CGRectMake(0, 0, ShopW, ShopH);
    [shopView addSubview:image];

}
```
###字典转模型
```objc
@property (nonatomic, strong) NSMutableArray *shops;

- (NSMutableArray *)shops
{
    if (_shops == nil) {

        _shops = [NSMutableArray array];

        NSArray *dicArray = [NSArray arrayWithContentsOfFile:[[NSBundle mainBundle] pathForResource:@"shops.plist" ofType:nil]];
        for (NSDictionary *dic in dicArray) {
            Shop *shop = [[Shop alloc] init];
            // _shop中存储的shop对象的地址
            // 所以，如果放在循环外声明shop对象，_shops存储的对象都是最后一个被赋值的shop的值
            shop.name = dic[@"name"];
            shop.icon = dic[@"icon"];
            // NSMutableArray有addObject
            [_shops addObject:shop];
        }
    }
    return _shop;
}
```
- ####模型的封装

```objc
@implementation Shop
// instancetype与id类似，能表示任何类型，只能用在返回值，使用这个返回值，编译器会检测返回值的真实类型，类型不匹配会警告
- (instancetype)initWithDict:(NSDictionary *)dict
{
    if (self = [super init])
    {
        self.name = dict[@"name"];
        self.icon = dict[@"icon"];
    }
    return self;
}

+ (instancetype)shopWithDict:(NSDictionary *)dict
{
    return [[self alloc] initWithDict:dict];
}
@end

```
