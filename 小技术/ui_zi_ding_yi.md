# UI 自定义

- ### 布局相关设置

```objc
// 带有navigationController的从导航栏下面开始布局
self.navigationController.navigationBar.translucent = NO;

// 当设置为YES时（默认YES），如果视图里面存在唯一一个UIScrollView或其子类View，那么它会自动设置相应的内边距，这样可以让scroll占据整个视图，又不会让导航栏遮盖。
self.automaticallyAdjustsScrollViewInsets = NO;

// iOS7当你的容器是navigation controller时，默认的布局将从navigation bar的顶部开始。这就是为什么所有的UI元素都往上漂移了44pt。(主要用于UISearchBar,UISearchDisplayController的搜索view偏移问题)
self.edgesForExtendedLayout = UIRectEdgeNone;

// 手动布局要用 masonry默认设置了
self.translatesAutoresizingMaskIntoConstraints = NO;

 // 禁止当前控制器滑动返回
self.navigationController.interactivePopGestureRecognizer.enabled = NO;
```

- ### Masonry小技巧

```objc
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

- ###UITabBar

```objc
# 1.使用继承UITabBar的自定义类，在UITabBarController中
[self setValue:[[MYTabBar alloc] init] forKeyPath:@"tabBar"];

# 2.UITabBarController中修改UITabBar的Frame（此方法不能修改高度）
self.tabBar.frame = CGRectMake(0,0,0,0);

# 3.修改UITabBar的高度
- (void)viewWillLayoutSubviews{
    CGRect tabFrame = self.tabBar.frame;
    // W=300 H=35
    tabFrame.size.height = 35;
    tabFrame.size.width = 300;
    // 距离屏幕底部20
    tabFrame.origin.y = self.view.height - tabFrame.size.height - 20;
    self.tabBar.frame = tabFrame;
    self.tabBar.centerX = self.view.centerX;
}
// 如果想要添加的工具条点击事件生效请在viewWillLayoutSubviews添加此代码（没啥问题，没试过）
[self.tabBar bringSubviewToFront:self.bottomToolView];

# 4.UITabBar布局子控件
- (void)layoutSubviews
{
    [super layoutSubviews];
    // 处理其他按钮
    CGFloat buttonW = self.width / 5;
    CGFloat i = 0;
    for (UIView *tabBarButton in self.subviews) {
        if (![NSStringFromClass(tabBarButton.class) isEqualToString:@"UITabBarButton"]) continue;
        tabBarButton.width = buttonW;
        tabBarButton.x = i * buttonW;
        if (i > 1) tabBarButton.x += buttonW;
        i++;
    }
}
```


- ###UITabBarItem
```objc
# 1. 只有图片时，位置显示不正确，调整
vc.tabBarItem.imageInsets = UIEdgeInsetsMake(8, 0, -8, 0);
```
- ###UItableView

```objc
# 1.去掉多余的cell
tableView.tableFooterView = [[UIView alloc] init];

// 上面不管用的话在cellForRowAtIndexPath中调用
-(void)setExtraCellLineHidden:(UITableView *)tableView
{
    UIView *view = [UIView new];
    view.backgroundColor = [UIColor clearColor];
    [tableView setTableFooterView:view];
}

# 2. UITableView的分割线调整左右间距(下面判断和代理都要实现)
    UIEdgeInsets edges = UIEdgeInsetsMake(0, 0, 0, 0);
    if ([tableView respondsToSelector:@selector(setSeparatorInset:)]) {
        [tableView setSeparatorInset:edges];
    }
    if ([tableView respondsToSelector:@selector(setLayoutMargins:)]) {
        [tableView setLayoutMargins:edges];
    }

// 代理
-(void)tableView:(UITableView *)tableView willDisplayCell:(UITableViewCell *)cell forRowAtIndexPath:(NSIndexPath *)indexPat
{
    UIEdgeInsets edges = UIEdgeInsetsMake(0, 0, 0, 0);
    if ([cell respondsToSelector:@selector(setLayoutMargins:)]) {
        [cell setLayoutMargins:edges];
    }
    if ([cell respondsToSelector:@selector(setSeparatorInset:)]){
        [cell setSeparatorInset:edges];
    }
}
# 3. 设置没有数据的View显示
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath
{
    if (_nearByArr.count == 0) {
        tableView.separatorStyle = UITableViewCellSeparatorStyleNone;
        static NSString *noMessageCellid = @"sessionnomessageCellidentifier";
        UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:noMessageCellid];
        if (cell == nil) {
            cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:noMessageCellid];
            UILabel *noMsgLabel = [[UILabel alloc] initWithFrame:CGRectMake(0.0f, 100.0f, cell.frame.size.width, 50.0f)];
            noMsgLabel.centerX = self.view.centerX;
            noMsgLabel.text = @"暂无数据";
            noMsgLabel.textColor = [UIColor darkGrayColor];
            noMsgLabel.textAlignment = NSTextAlignmentCenter;
            [cell.contentView addSubview:noMsgLabel];
            cell.selectionStyle = UITableViewCellSelectionStyleNone;
            cell.backgroundColor = [UIColor clearColor];
        }
        return cell;

    }
    // 有数据改回来格式
    tableView.separatorStyle = UITableViewCellSeparatorStyleSingleLine;
}
# 4.修改UITableViewHeaderView的背景色和文字颜色(FooterView同理也有自己的代理方法)
- (void)tableView:(UITableView *)tableView willDisplayHeaderView:(UIView *)view forSection:(NSInteger)section
{
    // Background color
    view.tintColor = COLOR_WITH_RGB(244, 245, 235);
    // Text Color
    // 文字颜色估计是在tableView:tableView titleForHeaderInSection:中设置的文字才行
    UITableViewHeaderFooterView *header = (UITableViewHeaderFooterView *)view;
    [header.textLabel setTextColor:COLOR_WITH_RGB(172, 150, 240)];
    
    // Another way to set the background color
    // Note: does not preserve gradient effect of original header
    // header.contentView.backgroundColor = [UIColor blackColor];
}
```
- ###UISearchBar

```objc
UISearchBar *searchBar = [[UISearchBar alloc] init];
# 1.设置searchBar背景色
// (1)backgroundColor 被遮住了，不行
searchBar.backgroundColor = [UIColor redColor];
// (2)
searchBar.barTintColor = [UIColor redColor];
// (3)如果barTintColor设置后底部有一条线，可以用背景图片
searchBar.backgroundImage = [UIImage imageNamed:@""];

// 去掉背景色只能
searchBar.backgroundImage = [UIImage imageWithColor:[UIColor clearColor]];
# 2.设置放大镜图标
[searchBar setImage:[UIImage imageNamed:@""] forSearchBarIcon:UISearchBarIconSearch state:UIControlStateNormal];
#3. 设置输入框（默认距离searchBar左右间距10）
// 设置背景(可以改输入框高度) 给个确定高度的图片可以改输入框高度，图片宽度要大于1
[searchBar setSearchFieldBackgroundImage:[UIImage imageNamed:@""] forState:UIControlStateNormal];
// 拿到搜索框内部的UITextField
UITextField *searchField = [searchBar valueForKey:@"_searchField"];
//    searchField.backgroundColor = [UIColor whiteColor];
//    searchField.borderStyle = UITextBorderStyleRoundedRect; // 设置圆角 背景图片什么的失效
//    searchField.layer.cornerRadius = 10;
//    searchField.layer.masksToBounds = YES;
// 修改输入文字颜色
searchField.textColor = COLOR_WITH_RGB(172, 150, 240);
// 修改占位文字的颜色
[searchField setValue:COLOR_WITH_RGB(172, 150, 240) forKeyPath:@"_placeholderLabel.textColor"];
# 4.UISearchDisplayController
// searchBar都是指自己创建的，不是UISearchDisplayController的searchBar(只读)
// 带有导航栏的，搜索控制器出现后向上移问题
@implementation CXSearchDisplayController
- (void)setActive:(BOOL)visible animated:(BOOL)animated
{
    [super setActive: visible animated: NO]; // 为了去掉切换的时候 导航栏闪过的黑色背景
    // 禁止隐藏导航栏
    [self.searchContentsController.navigationController setNavigationBarHidden: NO animated: NO];
}
@end
// 搜索控制器出来的时候，searchBar样式背景会变深灰色，如果searchBar单独布局在superView中背景色与superView一致是看不出来的）
// 这样输入框和搜索框同样大小，可以同时改searchBar的高度和setSearchFieldBackgroundImage的高度
searchBar.searchBarStyle = UISearchBarStyleMinimal;

# 5.禁止取消按钮（UISearchBar 结合UISearchDisplayController使用默认有取消按钮）
// 在 UISearchBarDelegate中实现
-(void)searchBarTextDidBeginEditing:(UISearchBar *)searchBar
{
    [searchBar setShowsCancelButton:NO animated:NO];
}

// 自定义UISearchDisplayController
@implementation CXSearchDisplayController
- (void)setActive:(BOOL)visible animated:(BOOL)animated
{
    [super setActive: visible animated: NO]; // 为了去掉切换的时候 导航栏闪过的黑色背景
    // 禁止隐藏导航栏
    [self.searchContentsController.navigationController setNavigationBarHidden: NO animated: NO];
}

# 6.Search Bar 怎样去掉背景的颜色(storyboard里只能设置background颜色，可是发现clear Color无法使用).其实在代码里还是可以设置的，那就是删除背景view
[[self.searchBar.subviews objectAtIndex: 0 ] removeFromSuperview];

---------------------------以下未测试------------------------------------------------
    1. UITextField *searchField;
    if ([[[UIDevice currentDevice] systemVersion] floatValue]<7.0)
        searchField=[searchBar.subviews objectAtIndex:1];
    else
        searchField=[((UIView *)[searchBar.subviews objectAtIndex:0]).subviews lastObject];
    
    5、修改UISearchBar右侧的取消按钮文字颜色及背景图片
    -(void)searchDisplayControllerWillBeginSearch:(UISearchDisplayController *)controller{
    [_searchBar setShowsCancelButton:YES animated:NO];
    UIView *topView = controller.searchBar.subviews[0];
    
    for (UIView *subView in topView.subviews) {
        if ([subView isKindOfClass:NSClassFromString(@"UINavigationButton")]) {
            UIButton *cancelButton = (UIButton*)subView;
            [cancelButton setTitle:@"取消" forState:UIControlStateNormal];  //@"取消"
        }
    }
}
        /*
     [self.searchBar setSearchFieldBackgroundPositionAdjustment:UIOffsetMake(30, 30)];// 设置搜索框中文本框的背景的偏移量
     [self.searchBar setSearchTextPositionAdjustment:UIOffsetMake(30, 0)];// 设置搜索框中文本框的文本偏移量
     [self.searchBar setInputAccessoryView:your_View];// 提供一个遮盖视图
     
     //键盘输入完成但未收起键盘
     - (void)searchBar:(UISearchBar *)searchBar textDidChange:(NSString *)searchText
     */
     -------------------------------------------------------------
```

- ###UITextField

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
    if (textField == _phoneField) {
        frame.size.width += 22;
    }else
    {
        frame.size.width += 16;
    }
    
    leftV.frame = frame;
    // 设置文本框的左边视图
    textField.leftView = leftV;
    // 必须要设置左边视图模式，总是显示模式
    textField.leftViewMode = UITextFieldViewModeAlways;
}
```
- ###UITextView 

```objc
// 解决中文输入 限定字数问题
- (void)textViewDidChange:(UITextView *)textView
{
    int kMaxLength = 15;
    NSString *toBeString = textView.text;
    NSString *lang = [[UITextInputMode currentInputMode] primaryLanguage];
    if([lang isEqualToString:@"zh-Hans"]){ //简体中文输入，包括简体拼音，健体五笔，简体手写
        UITextRange *selectedRange = [textView markedTextRange];
        UITextPosition *position = [textView positionFromPosition:selectedRange.start offset:0];
        if (!position){//非高亮
            if (toBeString.length > kMaxLength) {
                _textView.text = [toBeString substringToIndex:kMaxLength];
            }
        }
    }else{//中文输入法以外
        if (toBeString.length > kMaxLength) {
            _textView.text = [toBeString substringToIndex:kMaxLength];
        }
    }
}
```
- ###UINavigationBar 
```
    // 设置返回图标（不会拉伸）
    navBar.backIndicatorImage = [UIImage imageNamed:@""];
    navBar.backIndicatorTransitionMaskImage = [UIImage imageNamed:@""];
    // 取消返回按钮的默认文字
    [[UIBarButtonItem appearance] setBackButtonTitlePositionAdjustment:UIOffsetMake(0, -60) forBarMetrics:UIBarMetricsDefault];
```