## Xib 布局文件

xib --编译后--> nib
- 加载xib文件
```objc
// 文件名不加后缀
// 数组里面存储的是xib中的控件
// 方法1 加载Test.xib文件
    NSArray *array = [[NSBundle mainBundle] loadNibNamed:@"Test" owner:nil options:nil];
    [self.view addSubview:array[0]];
// 方法2
// bundle参数为nil默认为mainBundle
    UINib *nib = [UINib nibWithNibName:@"Test" bundle:[NSBundle mainBundle]];
    NSArray *array = [nib instantiateWithOwner:nil options:nil];
    [self.view addSubview:array[0]];
```

### `自定义控件从Xib，storyBoard加载`

从Xib加载控件会调用initWithCoder、awakeFromNib方法
通常在awakeFromNib实现一些初始化

### `代码自定义控件加载`
// 用alloc init, initWithFrame

（init方法也会调用initWithFrame）

会调用initWithFrame方法，在initWithFrame完成一些初始化
