在声明property属性后，有2种实现选择

@synthesize
编译器期间，让编译器自动生成getter/setter方法。
当有自定义的存或取方法时，自定义会屏蔽自动生成该方法

@dynamic
告诉编译器，不自动生成getter/setter方法，避免编译期间产生警告
然后由自己实现存取方法

或存取方法在运行时动态创建绑定：主要使用在CoreData的实现NSManagedObject子类时使用，由Core Data框架在程序运行的时动态生成子类属性

由于使用@dynamic，我们需要自己提供setter和getter方法。一般有两种方法：
- (1)自己提供setter和getter方法，将编译器自动生成的setter和getter方法手动再写一遍；
- (2)动态方法决议(DynamicMethod Resolution)，在运行时提供setter和getter对应实现的C函数。
    对于第一种方法，需要在类中显式提供实例变量，因为@dynamic不能像@synthesize那样向实现文件(.m)提供实例变量。

```objc
#import <Foundation/Foundation.h>

@interface Person : NSObject
{
    // must provide a ivar for our setter and getter
    NSString *_name;
}
@property (copy) NSString *name;
@end

@implementation Person
// @dynamic tells compiler don't generate setter and getter automatically
@dynamic name;

// We provide setter and getter here
- (void) setName:(NSString *)name
{
    if (_name != name) {
        [_name release];
        _name = [name copy];
    }
}

- (NSString *) name
{
    return _name;
}
@end // Person

int main(int argc, const char * argv[])
{
    NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
    
    Person *a = [[Person alloc] init];
    
    a.name = @"Hello"; // Ok, use our setter
    a.name = @"Hello, world";
    NSLog(@"%@", a.name); // Ok, use our getter
    
    [a release];
    [pool drain];
    
    return 0;
} // main
```
第二种方法，在运行时决定setter和getter对应实现的C函数，使用了NSObject提供的resolveInstanceMethod:方法。在C函数中不能直接使用实例变量，需要将ObjC对象self转成C中的结构体，因此在Person类同样需要显式声明实例变量而且访问级别是@public，为了隐藏该实例变量，将声明放在扩展(extension)中
```
#import <Foundation/Foundation.h>
#import <objc/objc-runtime.h> // for class_addMethod()

// ------------------------------------------------------
// A .h file
@interface Person : NSObject
@property (copy) NSString *name;
- (void) hello;
@end

// ------------------------------------------------------
// A .m file
// Use extension to override the access level of _name ivar
@interface Person ()
{
@public
    NSString *_name;
}
@end

@implementation Person
// @dynamic implies compiler to look for setName: and name method in runtime
@dynamic name;

// Only resolve unrecognized methods, and only load methods dynamically once
+ (BOOL) resolveInstanceMethod:(SEL)sel
{
    // Capture setName: and name method
    if (sel == @selector(setName:)) {
        class_addMethod([self class], sel, (IMP)setName, "v@:@");
/*
@encode()可以接受任何类型，Objective-C 中用这个指令做类型编码，它可以把任何一个类型转换为字符串，譬如：void 类型被编码之后为v，对象类型为@，SEL 类型为:等，具体的你可以参看Apple 官方页面关于Type Encoding 的描述：
http://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/ObjCRuntimeGuide/Articles/ocrtTypeEncodings.html#//apple_ref/doc/uid/TP40008048-CH100-SW
现在我们来正式的看以下第四个参数v@:f 的含义，它描述了IMP 指向的函数的描述信息，按照@encode指令编译之后的字符说明，第一个字符v 表示返回值为void，剩余的字符为dynamicMethod函数的参数描述，@表示第一个参数id，:自然就是第二个参数SEL，f就是第三个参数float。由于前面说过动态方法的实现的前两个参数必须是id、SEL，所以第四个参数中的字符串的第二、三个字符一定是@:。
*/
        return YES;
    }
    else if (sel == @selector(name)) {
        class_addMethod([self class], sel, (IMP)getName, "@@:");
        return YES;
    }
    
    return [super resolveInstanceMethod:sel];
}

void setName(id self, SEL _cmd, NSString* name)
{
    // Implement @property (copy)
    if (((Person *)self)->_name != name) {
        [((Person *)self)->_name release];
        ((Person *)self)->_name = [name copy];
    }
}

NSString* getName(id self, SEL _cmd)
{
    return ((Person *)self)->_name;
}

- (void) hello
{
    NSLog(@"Hello, world");
}

@end // Person

int main(int argc, const char * argv[])
{
    NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init];
    
    Person *a = [[Person alloc] init];
    [a hello]; // never call resolveInstanceMethod
    
    a.name = @"hello1";
    NSLog(@"%@", a.name);
    a.name = @"hello2";
    NSLog(@"%@", a.name);
    
    [a release];
    [pool drain];
    
    return 0;
} // main
```
Objective-C 的运行时只要发现你调用了@dynamic标注的属性的
setter、getter 方法，就会自动到resolveInstanceMethod 里去寻找真实的实现。实际上除了@dynamic标注的属性之外，如果你调用了类型中不存在的方法，也会被resolveInstanceMethod或者
resolveClassMethod截获，但由于你没有处理，所以会报告不能识别的消息的错误。一般Objective-C的运行时编程用到的并不多，除非你想设计一个动态化的功能，譬如：从网络下载一个升级包，不需要退出原有的程序，就可以动态的替换掉旧的功能等类似的需求。
