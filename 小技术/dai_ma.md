# 代码

```objc
//判断iphone6

#define IsiPhone6 ([UIScreen instancesRespondToSelector:@selector(currentMode)] ? CGSizeEqualToSize(CGSizeMake(750, 1334), [[UIScreen mainScreen] currentMode].size) : NO)


//判断iphone6+

#define IsiPhone6Plus ([UIScreen instancesRespondToSelector:@selector(currentMode)] ? CGSizeEqualToSize(CGSizeMake(1242, 2208), [[UIScreen mainScreen] currentMode].size) : NO)

#define UMSYSTEM_VERSION_GREATER_THAN_OR_EQUAL_TO(v)  ([[[UIDevice currentDevice] systemVersion] compare:v options:NSNumericSearch] != NSOrderedAscending)
#define _IPHONE80_ 80000



// 1134  640这是5 的   960 * 640这是 4的   480 * 320 这是4以下的

// 像素值可以得出不同手机宽高 1个像素是3gs  2像素是4s 5 3像素是6 6p

// 判断是否iPad
#define isPad (UI_USER_INTERFACE_IDIOM() == UIUserInterfaceIdiomPad)
/// not UIUserInterfaceIdiomPad
#define IS_IPHONE (UI_USER_INTERFACE_IDIOM() == UIUserInterfaceIdiomPhone)
/// detect iPhone6 Plus based on its native scale
#define IS_IPHONE_6PLUS (IS_IPHONE && [[UIScreen mainScreen] nativeScale] == 3.0f)

#define IS_IPHONE_3_5Inche (IS_IPHONE && [UIScreen mainScreen].bounds.size.width == 320 && [UIScreen mainScreen].bounds.size.height == 480)
#define IS_IPHONE_4_0Inche (IS_IPHONE && [UIScreen mainScreen].bounds.size.width == 320 && [UIScreen mainScreen].bounds.size.height == 568)
#define IS_IPHONE_4_7Inche (IS_IPHONE && [UIScreen mainScreen].bounds.size.width == 375 && [UIScreen mainScreen].bounds.size.height == 667)
#define IS_IPHONE_5_5Inche (IS_IPHONE && [UIScreen mainScreen].bounds.size.width == 414 && [UIScreen mainScreen].bounds.size.height == 736)
```