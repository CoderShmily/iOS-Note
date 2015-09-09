## UILable
##### label对象的创建

```objc
UILabel *label = [[UILabel alloc] init];  // 创建label对象
label.frame = CGRectMake(100, 100, 200, 100); // 设置label大小

UILabel *label2 = [[UILabel alloc] initWithFrame:CGRectMake(0, 0, 80, 80)];
```

#### label常见属性
- @property(nonatomic,copy) NSString *text; // 显示的文字
- @property(nonatomic,retain) UIFont *font; // 字体
- @property(nonatomic,retain) UIColor *textColor; // 文字颜色
- @property(nonatomic) NSTextAlignment textAlignment; // 对齐模式
- @property(nonatomic) NSInteger numberOfLines; // 文字行数
- @property(nonatomic) NSLineBreakMode lineBreakMode; // 换行模式
- @property(nonatomic,copy) UIColor *backgroundColor； // 背景色

#### label属性修改说明
- label的文本

```objc
label.text = @"hello world hello";
```

- label的字体

```objc
- UIColor *color = [UIColor redColor];

// 有以下类方法可选
+ (UIColor *)blackColor;      // 0.0 white
+ (UIColor *)darkGrayColor;   // 0.333 white
+ (UIColor *)lightGrayColor;  // 0.667 white
+ (UIColor *)whiteColor;      // 1.0 white
+ (UIColor *)grayColor;       // 0.5 white
+ (UIColor *)redColor;        // 1.0, 0.0, 0.0 RGB
+ (UIColor *)greenColor;      // 0.0, 1.0, 0.0 RGB
+ (UIColor *)blueColor;       // 0.0, 0.0, 1.0 RGB
+ (UIColor *)cyanColor;       // 0.0, 1.0, 1.0 RGB
+ (UIColor *)yellowColor;     // 1.0, 1.0, 0.0 RGB
+ (UIColor *)magentaColor;    // 1.0, 0.0, 1.0 RGB
+ (UIColor *)orangeColor;     // 1.0, 0.5, 0.0 RGB
+ (UIColor *)purpleColor;     // 0.5, 0.0, 0.5 RGB
+ (UIColor *)brownColor;      // 0.6, 0.4, 0.2 RGB
+ (UIColor *)clearColor;      // 0.0 white, 0.0 alpha
```

- label中字体的对齐

```objc
label.textAlignment = NSTextAlignmentRight;

// 有以下对齐方式可选
NSTextAlignmentLeft      = 0,    // Visually left aligned
NSTextAlignmentCenter    = 1,    // Visually centered
NSTextAlignmentRight     = 2,    // Visually right aligned
NSTextAlignmentJustified = 3,    // Fully-justified. The last line in a paragraph is natural-aligned.
NSTextAlignmentNatural   = 4,
```

- label中字体的大小

```objc
label.font = [UIFont boldSystemFontOfSize:20];

// 有以下类方法可选
+ (UIFont *)systemFontOfSize:(CGFloat)fontSize;   系统默认字体
+ (UIFont *)boldSystemFontOfSize:(CGFloat)fontSize;  粗体
+ (UIFont *)italicSystemFontOfSize:(CGFloat)fontSize;  斜体
```
- label中文本的行数
	- 行数等于0 则为自动扩展

```objc
label.numberOfLines = 2;
```

- 换行模式
	- NSLineBreakByWordWrapping = 0,     	// 无省略号 多行中，按字母显示
    - NSLineBreakByCharWrapping,		// 无省略号 多行中，按单词显示
    - NSLineBreakByClipping,		// 无省略号 显示不完则截断
    - NSLineBreakByTruncatingHead,	// "...wxyz"
    - NSLineBreakByTruncatingTail,	// "abcd..."
    - NSLineBreakByTruncatingMiddle	// "ab...yz"

```objc
label.text = @"hello world hello";
label.lineBreakMode = NSLineBreakByCharWrapping;
```
####范例
```objc
UILabel *label = [[UILabel alloc] init];
label.backgroundColor = [UIColor redColor];
label.frame = CGRectMake(100, 100, 200, 100);
label.text = @"hello world!hello world!hello world!hello world!hello world!hello world!";
self.label.textColor = [UIColor redColor];
label.textAlignment = NSTextAlignmentRight;
label.lineBreakMode = NSLineBreakByCharWrapping;
label.numberOfLines = 2;
label.font = [UIFont boldSystemFontOfSize:20];
[self.view addSubview:label];
```

 `[TOC]`
