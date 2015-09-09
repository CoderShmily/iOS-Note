####更改启动图片

```
1.可以在LaunchScreen.xib中添加UIImageView布局适应屏幕大小

2.先取消Launch Screen File中的LaunchScreen设置,在项目设置 的Launch Images Source设置,然后把图片拉入Images.xcassets的 LaunchImage中
```
#### 设置文本框左边视图
`文本框的代理方法说明`
```objc
- (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string;
//只能拦截不能监听改变(一个字符不显示,输入第二个字符显示第一个字符)

```
```objc
// 设置文本框左边视图
- (void)setUpTextFieldLeftView:(UITextField *)textField image:(UIImage *)image
{
    // 设置账号文本框左边视图
    // 如果是系统控件中的子控件通常位置是由系统决定，我们不能设置，但是我们可以设置它的尺寸。
    UIImageView *leftV = [[UIImageView alloc] initWithImage:image];

    // 设置图片居中
    leftV.contentMode = UIViewContentModeCenter;

    CGRect frame = leftV.frame;
    frame.size.width += 10;
    leftV.frame = frame;

    // 设置文本框的左边视图
    textField.leftView = leftV;
    // 必须要设置左边视图模式，总是显示模式
    textField.leftViewMode = UITextFieldViewModeAlways;
}
```

