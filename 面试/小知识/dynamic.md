在声明property属性后，有2种实现选择

- @synthesize
编译器期间，让编译器自动生成getter/setter方法。
当有自定义的存或取方法时，自定义会屏蔽自动生成该方法

- @dynamic
告诉编译器，不自动生成getter/setter方法，避免编译期间产生警告
然后由自己实现存取方法或存取方法在运行时动态创建绑定。

由于使用@dynamic，我们需要自己提供setter和getter方法。一般有两种方法：

1). 自己提供setter和getter方法，将编译器自动生成的setter和getter方法手动再写一遍；<br>注意：需要在类中显式提供实例变量，因为@dynamic不能像@synthesize那样向实现文件(.m)提供实例变量。

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
2). 动态方法决议(DynamicMethod Resolution)，在运行时提供setter和getter对应实现的C函数。


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

//+ (BOOL)resolveClassMethod:(SEL)name
//{
//    NSLog(@" >> Class resolving %@", NSStringFromSelector(name));
//    
//    return [super resolveClassMethod:name];
//}


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


让我们来看看运行时系统是如何进行动态方法决议的，下面的代码来自苹果官方公开的源码 objc-class.mm，我在其中添加了中文注释：

1，首先是判断是不是要进行类方法决议，如果不是或决议失败，则进行实例方法决议
```objc
/**********************************************************/
* _class_resolveMethod
* Call +resolveClassMethod or +resolveInstanceMethod and return 
* the method added or NULL. 
* Assumes the method doesn't exist already.
***********************************************************/
__private_extern__ Method _class_resolveMethod(Class cls, SEL sel)
{
    Method meth = NULL;

    if (_class_isMetaClass(cls)) {
        meth = _class_resolveClassMethod(cls, sel);
    }
    if (!meth) {
        meth = _class_resolveInstanceMethod(cls, sel);
    }

    if (PrintResolving  &&  meth) {
        _objc_inform("RESOLVE: method %c[%s %s] dynamically resolved to %p", 
                     class_isMetaClass(cls) ? '+' : '-', 
                     class_getName(cls), sel_getName(sel), 
                     method_getImplementation(meth));
    }
    
    return meth;
}
```
2，类方法决议与实例方法决议大体相似，在这里就只看实例方法决议部分了：
```objc
 /***********************************************************************
 * _class_resolveInstanceMethod
 * Call +resolveInstanceMethod and return the method added or NULL.
 * cls should be a non-meta class.
 * Assumes the method doesn't exist already.
 **********************************************************************/
static Method _class_resolveInstanceMethod(Class cls, SEL sel)
{
    BOOL resolved;
    Method meth = NULL;
    
    // 是否实现了 resolveInstanceMethod，如果没有返回 NULL
    if (!look_up_method(((id)cls)->isa, SEL_resolveInstanceMethod, 
                        YES /*cache*/, NO /*resolver*/))
    {
        return NULL;
    }
    
    // 调用 resolveInstanceMethod，并获取返回值
    resolved = ((BOOL(*)(id, SEL, SEL))objc_msgSend)((id)cls, SEL_resolveInstanceMethod, sel);
    
    if (resolved) {
        // 返回值为 YES，表示 resolveInstanceMethod 声称它已经成功添加实现，则再次查找 method list 
        // +resolveClassMethod adds to self
        meth = look_up_method(cls, sel, YES/*cache*/, NO/*resolver*/);
        
        if (!meth) {
            // resolveInstanceMethod 使诈了，它声称成功添加实现了，但实际没有，给出警告信息，并返回 NULL
            // Method resolver didn't add anything?
            _objc_inform("+[%s resolveInstanceMethod:%s] returned YES, but "
                         "no new implementation of %c[%s %s] was found", 
                         class_getName(cls),
                         sel_getName(sel), 
                         class_isMetaClass(cls) ? '+' : '-', 
                         class_getName(cls), 
                         sel_getName(sel));
            return NULL;
        }
    }
    
    // 其他情况下返回 NULL
    return meth;
}  
```

这段代码很容易理解：
1. 首先判断是否实现了 resolveInstanceMethod，如果没有实现，返回 NULL，进入下一步处理；
2. 如果实现了，调用 resolveInstanceMethod，获取返回值；
3. 如果返回值为 YES，表示 resolveInstanceMethod 声称它已经提供了 selector 的实现，因此再次查找 method list，如果依然找到对应的IMP，则返回该实现，否则提示警告信息，返回 NULL，进入下一步处理；
4. 如果返回值为 NO，返回 NULL，进入下一步处理；

#### 加入消息转发

在前文[《深入浅出Cocoa之消息》](http://www.cnblogs.com/kesalin/archive/2011/08/15/objc_method_base.html)，我演示了一个消息转发的示例，下面我把动态方法决议部分去除，把消息转发部分添加进来：
```objc
// Proxy
@interface Proxy : NSObject

-(void)MissMethod;

@end

@implementation Proxy

-(void)MissMethod
{
    NSLog(@" >> MissMethod() called in Proxy.");
}

@end

// Foo
@interface Foo : NSObject

-(void)Bar;

@end

@implementation Foo

- (void)forwardInvocation:(NSInvocation *)anInvocation
{
    SEL name = [anInvocation selector];
    NSLog(@" >> forwardInvocation for selector %@", NSStringFromSelector(name));
    
    Proxy * proxy = [[[Proxy alloc] init] autorelease];
    if ([proxy respondsToSelector:name]) {
        [anInvocation invokeWithTarget:proxy];
    }
    else {
        [super forwardInvocation:anInvocation];
    }
}

- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector {
    return [Proxy instanceMethodSignatureForSelector:aSelector];
}

-(void)Bar
{
    NSLog(@" >> Bar() in Foo.");
}

@end
```
运行测试代码，输出如下：
```
  >> Bar() in Foo.
  >> forwardInvocation for selector MissMethod
  >> MissMethod() called in Proxy.
```

如果我把动态方法决议部分代码也加入进来输出又是怎样呢？下面只列出了 Foo 的实现代码，其他代码不变动。
```objc
@implementation Foo

+(BOOL)resolveInstanceMethod:(SEL)name
{
    NSLog(@" >> Instance resolving %@", NSStringFromSelector(name));
    
    if (name == @selector(MissMethod)) {
        class_addMethod([self class], name, (IMP)dynamicMethodIMP, "v@:");
        return YES;
    }
    
    return [super resolveInstanceMethod:name];
}

+(BOOL)resolveClassMethod:(SEL)name
{
    NSLog(@" >> Class resolving %@", NSStringFromSelector(name));
    return [super resolveClassMethod:name];
}

- (void)forwardInvocation:(NSInvocation *)anInvocation
{
    SEL name = [anInvocation selector];
    NSLog(@" >> forwardInvocation for selector %@", NSStringFromSelector(name));
    
    Proxy * proxy = [[[Proxy alloc] init] autorelease];
    if ([proxy respondsToSelector:name]) {
        [anInvocation invokeWithTarget:proxy];
    }
    else {
        [super forwardInvocation:anInvocation];
    }
}

- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector {
    return [Proxy instanceMethodSignatureForSelector:aSelector];
}

-(void)Bar
{
    NSLog(@" >> Bar() in Foo.");
}

@end
```