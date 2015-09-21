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

- 相关的定义

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
- 类在runtime中的表示

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
- 获取列表(属性、方法、协议) 

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
- 方法调用在运行时的过程

```

```