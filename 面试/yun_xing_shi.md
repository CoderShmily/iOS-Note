```objc
class_getInstanceMethod();

// 通过class_addMethod方法将一个函数加入到类的方法中
class_addMethod(__unsafe_unretained Class cls, SEL name, IMP imp, const char *types);

method_getImplementation();
method_getTypeEncoding();
method_exchangeImplementations;

```

```objc
objc_setAssociatedObject
objc_getAssociatedObject
objc_removeAssociatedObjects

object_getClass(id obj);
object_getClassName(id obj);
object_getIvar(id obj, Ivar ivar);
object_isClass(id obj)
     
object_setClass(id obj, __unsafe_unretained Class cls)
object_setIvar(id obj, Ivar ivar, id value)

objc_send(id, SEL, ...) //发送消息
objc_msgSend(id, SEL);
```

#### 相关的定义

```objc
/// 描述类中的一个方法
typedef struct objc_method *Method;

/// 实例变量
typedef struct objc_ivar *Ivar;

/// 类别Category
typedef struct objc_category *Category;

/// 类中声明的属性
typedef struct objc_property *objc_property_t;
```
#### 类在runtime中的表示

```
struct objc_class {
    Class isa;//指针，顾名思义，表示是一个什么，
    //实例的isa指向类对象，类对象的isa指向元类

#if !__OBJC2__
    Class super_class;  //指向父类
    const char *name;  //类名
    long version;
    long info;
    long instance_size
    struct objc_ivar_list *ivars //成员变量列表
    struct objc_method_list **methodLists; //方法列表
    struct objc_cache *cache;//缓存
    //一种优化，调用过的方法存入缓存列表，下次调用先找缓存
    struct objc_protocol_list *protocols //协议列表
    #endif
} OBJC2_UNAVAILABLE;
/* Use `Class` instead of `struct objc_class *` */
```
#### 获取列表(属性、方法、协议) 

```
#import <objc/runtime.h>

 unsigned int count;
    //获取属性列表
    objc_property_t *propertyList = class_copyPropertyList([self class], &count);
    for (unsigned int i=0; i<count; i++) {
        const char *propertyName = property_getName(propertyList[i]);
        NSLog(@"property---->%@", [NSString stringWithUTF8String:propertyName]);
    }

    //获取方法列表
    Method *methodList = class_copyMethodList([self class], &count);
    for (unsigned int i; i<count; i++) {
        Method method = methodList[i];
        NSLog(@"method---->%@", NSStringFromSelector(method_getName(method)));
    }

    //获取成员变量列表
    Ivar *ivarList = class_copyIvarList([self class], &count);
    for (unsigned int i; i<count; i++) {
        Ivar myIvar = ivarList[i];
        const char *ivarName = ivar_getName(myIvar);
        NSLog(@"Ivar---->%@", [NSString stringWithUTF8String:ivarName]);
    }

    //获取协议列表
    __unsafe_unretained Protocol **protocolList = class_copyProtocolList([self class], &count);
    for (unsigned int i; i<count; i++) {
        Protocol *myProtocal = protocolList[i];
        const char *protocolName = protocol_getName(myProtocal);
        NSLog(@"protocol---->%@", [NSString stringWithUTF8String:protocolName]);
    }
```
#### 方法调用在运行时的过程

- 如果调用实例方法，会到实例的isa指针指向的对象（也就是类对象）操作。
- 如果调用的是类方法，会到类对象的isa指针指向的对象（也就是元类对象）中操作。

    1) 在相应操作的对象中的缓存方法列表中找调用的方法，如果找到，转向相应实现并执行。<br>
    2) 如果没找到，在相应操作的对象中的方法列表中找调用的方法，如果找到，转向相应实现执行<br>
    3) 如果没找到，去父类指针所指向的对象中执行1，2.<br>
    4) 以此类推，如果一直到根类还没找到，转向`拦截调用`。<br>
    5) 如果没有重写`拦截调用`的方法，程序报错。<br>

#### 拦截调用

拦截调用就是，在找不到调用的方法程序崩溃之前，你有机会通过重写NSObject的四个方法来处理。
```
+ (BOOL)resolveClassMethod:(SEL)sel;
+ (BOOL)resolveInstanceMethod:(SEL)sel;
//后两个方法需要转发到其他的类处理
- (id)forwardingTargetForSelector:(SEL)aSelector;
- (void)forwardInvocation:(NSInvocation *)anInvocation;
```
- 第一个方法是当你调用一个不存在的类方法的时候，会调用这个方法，默认返回NO，你可以加上自己的处理然后返回YES。
- 第二个方法和第一个方法相似，只不过处理的是实例方法。
- 第三个方法是将你调用的不存在的方法重定向到一个其他声明了这个方法的类，只需要你返回一个有这个方法的target。
- 第四个方法是将你调用的不存在的方法打包成NSInvocation传给你。做完你自己的处理后，调用invokeWithTarget:方法让某个target触发这个方法。