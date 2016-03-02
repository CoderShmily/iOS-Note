# iOS7 8 9适配





## iOS8注意
===================

#### 1.针对屏幕适配应运而生的size classes

>##### size classes是为了解决storyboard只能订制一种屏幕样式的问题，它不再是具体的尺寸，而是抽象尺寸通过宽/高 的compact、any、regular 组成了九种组合包含了所有苹果设备的尺寸。

#### 2.获取用户授权的用户隐私保护

- 地图定位示例

```objc
// 导入定位框架
#import <CoreLocation/CoreLocation.h>

@interface ViewController ()<CLLocationManagerDelegate>

// 设置定位对象
@property(nonatomic,strong)CLLocationManager* maneger;

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    // 当使用iOS8定位的时候需要请求用户授权，且在info.plist里添加字段NSLocationAlwaysUsageDescription 请求用户授权的描述
    // iOS7仅仅需要在info.plist里添加字段Privacy - Location Usage Description 请求用户授权的描述
    // 不需要再写下面的代码
    if (IOS8) {
        [self.maneger requestAlwaysAuthorization];//请求用户授权 
    }

    // 开启定位
    [self.maneger startUpdatingLocation];

}
```
#### 3.UIAlertController对alert&actionSheet的封装
###### 添加按钮、添加文本输入框

#### 4.远程推送
>##### 在iOS8.0中，如果要使用本地通知，需要得到用户的许可,在didFinishLaunchingWithOptions方法中添加如下代码：

```objc
// 8.0以前和以后的区别
UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeBadge | UIUserNotificationTypeSound | UIUserNotificationTypeAlert categories:nil];
[application registerUserNotificationSettings:settings];
```
---
##iOS9注意

>##### 虽然现在的iOS9已经推送正式版了，但是iOS9使用时还是会感觉到App比以前更加卡顿了，tableView拖动时卡顿显示的最为明显。 并且之前遇到一个bug，原本好的项目用xcode7一编译，tableView刷新出了问题 ，[tableView reloadData]无效，有一行cell明明改变了但是刷新不出来。 感觉可能是这个方法和某种新加的特性冲突了，猜测可能是reloadData的操作被推迟到下一个RunLoop执行最终失效。
解决的方法是，注释[tableView reloadData]，改用局部刷新，问题居然就解决了。

####1.网络适配 HTTPS
---
##iPad适配
####iPad适配Slide Over 和 Split View

>##### Over 和 Split View，若想适配multi tasking特性，唯一的建议：弃纯代码，改用storyboard、xib，纵观苹果WWDC所有Demo均是如此。
