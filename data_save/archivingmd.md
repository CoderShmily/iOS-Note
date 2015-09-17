# archiving

#### 归档:自定义对象想要存储到沙盒（文件夹），必须通过归档。
`自定义对象要归档，必须要遵守NSCoding协议`
```objc
- (IBAction)save:(id)sender {

    Person *person = [[Person alloc] init];
    person.age = 10;
    person.name = @"adas";
    
    // 获取temp文件夹路径
    NSString *tempPath = NSTemporaryDirectory();

    // 获取Document文件夹路径
//    NSString *docPath = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES)[0];

    // 拼接文件全路径
    NSString *filePath = [tempPath stringByAppendingPathComponent:@"person.data"];

    // 归档的时候让person对象调用encodeWithCoder
    // 传入多个参数，可以传入数组
    [NSKeyedArchiver archiveRootObject:person toFile:filePath];


}
- (IBAction)read:(id)sender {
    // 获取temp文件夹路径
    NSString *tempPath = NSTemporaryDirectory();

    // 获取Document文件夹路径
    //    NSString *docPath = NSSearchPathForDirectoriesInDomains(NSDocumentDirectory, NSUserDomainMask, YES)[0];

    // 拼接文件全路径
    NSString *filePath = [tempPath stringByAppendingPathComponent:@"person.data"];
   Person *p =  [NSKeyedUnarchiver unarchiveObjectWithFile:filePath];

    NSLog(@"%@",p.name);
}

```
#### Person类实现

```objc
//  Person.h
#import <Foundation/Foundation.h>
@interface Person : NSObject<NSCoding>

@property (nonatomic, assign) int age;
@property (nonatomic, strong) NSString *name;

@end
```
```objc
//  Person.m
#import "Person.h"

// 自定义对象要归档，必须要遵守NSCoding协议
@implementation Person

// 什么时候调用：当自定义对象归档的时候调用
// 作用：告诉系统当前对象哪些属性需要归档
- (void)encodeWithCoder:(NSCoder *)aCoder
{
    [aCoder encodeObject:_name forKey:@"name"];

    [aCoder encodeInt:_age forKey:@"age"];
}

// init初始化方法,先要初始化父类
// 什么时候调用：当自定义对象解档的时候调用
// 作用:告诉系统当前对象哪些属性需要解档

// 当解析一个文件的时候，就会调用initWithCoder
- (id)initWithCoder:(NSCoder *)aDecoder
{
#warning 什么时候调用initWithCoder初始化父类
    if (self = [super init]) {

        // 解档属性
        // 解档属性一定要记得保存到成员属性里
      _name = [aDecoder decodeObjectForKey:@"name"];

        _age = [aDecoder decodeIntForKey:@"age"];

    }
    return self;
}

@end
```
#### initWithCoder补充

```objc
#import "RedView.h"
// @interface RedView : UIView

@implementation RedView

// 解档：肯定会调用对象initWithCoder:,做父类的初始化操作
// 什么时候调用[super initWithCoder:aDecoder]:只要父类遵守了NSCoding协议，就需要调用

- (id)initWithCoder:(NSCoder *)aDecoder
{
    // super -> UIView
    if (self = [super initWithCoder:aDecoder]) {
        NSLog(@"%s",__func__);
    }
    return self;
}
@end
```
