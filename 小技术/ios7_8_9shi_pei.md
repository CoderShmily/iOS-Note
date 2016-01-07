# iOS7 8 9适配





iOS8注意
===================

##### 针对屏幕适配应运而生的size classes

size classes是为了解决storyboard只能订制一种屏幕样式的问题，它不再是具体的尺寸，而是抽象尺寸通过宽/高 的compact、any、regular 组成了九种组合包含了所有苹果设备的尺寸。

##### 获取用户授权的用户隐私保护

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