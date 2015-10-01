
#### Runtime方法列表:
```objc
# class
class_getInstanceMethod -> Method class_getInstanceMethod(Class cls, SEL name);// 返回给定类的指定的实例方法
class_addMethod -> BOOL class_addMethod(Class cls, SEL name, IMP imp, const char *types);// 通过方法名SEL+原来的IMP实现给类添加新方法

class_copyPropertyList -> objc_property_t *class_copyPropertyList(Class cls, unsigned int *outCount) // 获取类的属性列表
property_getName -> const char *property_getName(objc_property_t property) // 传入上面的数组(指针)获取每个属性的名称

# method
method_getTypeEncoding -> const char *method_getTypeEncoding(Method m);// Returns a string describing a method's parameter and return types.
method_getImplementation -> IMP method_getImplementation(Method m);//获取Method中的IMP
method_exchangeImplementations -> method_exchangeImplementations(Method m1, Method m2); //Returns a string describing a method's parameter and return types.
method_setImplementation -> IMP method_setImplementation(Method m, IMP imp);// Sets the implementation of a method.

# objc
# 关联对象
objc_setAssociatedObject -> objc_setAssociatedObject(id object, const void *key, id value, objc_AssociationPolicy policy);//Sets an associated value for a given object using a given key and association policy.
objc_getAssociatedObject -> id objc_getAssociatedObject(id object, const void *key);// Returns the value associated with a given object for a given key.
objc_removeAssociatedObjects -> objc_removeAssociatedObjects(id object);// 注意:Removes all associations for a given object.

# 类
object_isClass -> BOOL object_isClass(id obj);//Returns whether an object is a class object.
object_getClass -> Class object_getClass(id obj);//Returns the class of an object.
object_getClassName -> const char *object_getClassName(id obj);//Returns the class name of a given object.
object_setClass -> Class object_setClass(id obj, Class cls);//Sets the class of an object.
objc_getMetaClass -> Class objc_getMetaClass(const char *name);// 原类

# 实例变量
object_getIvar -> id object_getIvar(id obj, Ivar ivar);//Reads the value of an instance variable in an object.
object_setIvar -> object_setIvar(id obj, Ivar ivar, id value);//Sets the value of an instance variable in an object.

// Changes the value of an 实例变量 of a 实例
object_getInstanceVariable -> Ivar object_getInstanceVariable(id obj, const char *name, void **outValue)
object_setInstanceVariable -> Ivar object_setInstanceVariable(id obj, const char *name, void *value);

# 发送消息
objc_msgSend -> objc_msgSend(id obj, SEL name);
```
#### 获取列表(属性、方法、协议) 

```objc
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

```objc
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

#### 方法调用在运行时的过程

- 如果调用实例方法，会到实例的isa指针指向的对象（也就是类对象）操作。
- 如果调用的是类方法，会到类对象的isa指针指向的对象（也就是元类对象）中操作。

    1. 在相应操作的对象中的缓存方法列表中找调用的方法，如果找到，转向相应实现并执行。<br>
    2. 如果没找到，在相应操作的对象中的方法列表中找调用的方法，如果找到，转向相应实现执行<br>
    3. 如果没找到，去父类指针所指向的对象中执行1，2.<br>
    4. 以此类推，如果一直到根类还没找到，转向`拦截调用`。<br>
    5. 如果没有重写`拦截调用`的方法，程序报错。<br>

#### 拦截调用

拦截调用就是，在找不到调用的方法程序崩溃之前，你有机会通过重写NSObject的四个方法来处理。
```objc
1.
// 动态解析 类方法
+ (BOOL)resolveClassMethod:(SEL)sel;
// 动态解析 实例方法
+ (BOOL)resolveInstanceMethod:(SEL)sel;
 2.
 // 当某个对象不能接受某个selector时，将对该selector的调用转发给另一个对象
 - (id)forwardingTargetForSelector:(SEL)aSelector
3.
// 后两个方法需要转发消息到其他类时要同时实现
//传入参数NSInvocation对象对前一个接口返回的方法对象是有依赖，前一个接口的NSMethodSignature对象返回nil，则消息转发流程即告结束
- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector
- (void)forwardInvocation:(NSInvocation *)anInvocation;
```
- 第一个方法是当你调用一个不存在的类方法的时候，会调用这个方法，默认返回NO，你可以加上自己的处理然后返回YES。
- 第二个方法和第一个方法相似，只不过处理的是实例方法。
- 第三个方法是将你调用的不存在的方法重定向到一个其他声明了这个方法的类，只需要你返回一个有这个方法的target。
- 第四个方法是将你调用的不存在的方法打包成NSInvocation传给你。做完你自己的处理后，调用invokeWithTarget:方法让某个target触发这个方法。

#### 动态添加方法
重写了拦截调用的方法并且返回了YES，我们要怎么处理呢？<br>
有一个办法是根据传进来的SEL类型的selector动态添加一个方法。

首先从外部隐式调用一个不存在的方法：
```objc
//隐式调用方法
[target performSelector:@selector(resolveAdd:) withObject:@"test"];
```
然后，在target对象内部重写拦截调用的方法，动态添加方法。
```objc
void runAddMethod(id self, SEL _cmd, NSString *string){
    NSLog(@"add C IMP ", string);
}
+ (BOOL)resolveInstanceMethod:(SEL)sel{

    //给本类动态添加一个方法
    if ([NSStringFromSelector(sel) isEqualToString:@"resolveAdd:"]) {
        class_addMethod(self, sel, (IMP)runAddMethod, "v@:*");
    }
    return YES;
}
```
其中class_addMethod的四个参数分别是：<br>
1. Class cls 给哪个类添加方法，本例中是self<br>
2. SEL name 添加的方法，本例中是重写的拦截调用传进来的selector。<br>
3. IMP imp 方法的实现，C方法的方法实现可以直接获得。如果是OC方法，可以用<br>+ (IMP)instanceMethodForSelector:(SEL)aSelector;获得方法的实现。<br>
4. "v@:*"方法的签名，代表有一个参数的方法。

#### 关联对象
现在你准备用一个系统的类，但是系统的类并不能满足你的需求，你需要额外添加一个属性。<br>
这种情况的一般解决办法就是继承。<br>
但是，只增加一个属性，就去继承一个类，总是觉得太麻烦类。<br>
这个时候，runtime的关联属性就发挥它的作用了。
```objc
//首先定义一个全局变量，用它的地址作为关联对象的key
static char associatedObjectKey;
//设置关联对象
objc_setAssociatedObject(target, &associatedObjectKey, @"添加的字符串属性", OBJC_ASSOCIATION_RETAIN_NONATOMIC); //获取关联对象
NSString *string = objc_getAssociatedObject(target, &associatedObjectKey);
NSLog(@"AssociatedObject = %@", string);
```
objc_setAssociatedObject的四个参数：

1. id object给谁设置关联对象。
2. const void *key关联对象唯一的key，获取时会用到。
3. id value关联对象。
4. objc_AssociationPolicy关联策略，有以下几种策略：

```objc
enum {
    OBJC_ASSOCIATION_ASSIGN = 0,
    OBJC_ASSOCIATION_RETAIN_NONATOMIC = 1, 
    OBJC_ASSOCIATION_COPY_NONATOMIC = 3,
    OBJC_ASSOCIATION_RETAIN = 01401,
    OBJC_ASSOCIATION_COPY = 01403 
};
```
objc_getAssociatedObject的两个参数。
1. id object获取谁的关联对象。
2. const void *key根据这个唯一的key获取关联对象。

其实，你还可以把添加和获取关联对象的方法写在你需要用到这个功能的类的类别中，方便使用。
```objc
//添加关联对象
- (void)addAssociatedObject:(id)object{
    objc_setAssociatedObject(self, @selector(getAssociatedObject), object, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
}
//获取关联对象
- (id)getAssociatedObject{
    return objc_getAssociatedObject(self, _cmd);
}
```

###### 注意：这里面我们把getAssociatedObject方法的地址作为唯一的key，_cmd代表当前调用方法的地址。

#### 方法交换
方法交换，就是将两个方法的实现交换。例如，将A方法和B方法交换，调用A方法的时候，就会执行B方法中的代码，反之亦然。<br>
话不多说，这是参考Mattt大神在NSHipster上的文章自己写的代码。
```objc
#import "UIViewController+swizzling.h"
#import <objc/runtime.h>

@implementation UIViewController (swizzling)

//load方法会在类第一次加载的时候被调用
//调用的时间比较靠前，适合在这个方法里做方法交换
+ (void)load{
    //方法交换应该被保证，在程序中只会执行一次
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{

        //获得viewController的生命周期方法的selector
        SEL systemSel = @selector(viewWillAppear:);
        //自己实现的将要被交换的方法的selector
        SEL swizzSel = @selector(swiz_viewWillAppear:);
        //两个方法的Method
        Method systemMethod = class_getInstanceMethod([self class], systemSel);
        Method swizzMethod = class_getInstanceMethod([self class], swizzSel);

        //首先动态添加方法，实现是被交换的方法，返回值表示添加成功还是失败
        BOOL isAdd = class_addMethod(self, systemSel, method_getImplementation(swizzMethod), method_getTypeEncoding(swizzMethod));
        if (isAdd) {
            //如果成功，说明类中不存在这个方法的实现
            //将被交换方法的实现替换到这个并不存在的实现
            class_replaceMethod(self, swizzSel, method_getImplementation(systemMethod), method_getTypeEncoding(systemMethod));
        }else{
            //否则，交换两个方法的实现
            method_exchangeImplementations(systemMethod, swizzMethod);
        }

    });
}

- (void)swiz_viewWillAppear:(BOOL)animated{
    //这时候调用自己，看起来像是死循环
    //但是其实自己的实现已经被替换了
    [self swiz_viewWillAppear:animated];
    NSLog(@"swizzle");
}

@end
```
在一个自己定义的viewController中重写viewWillAppear
```objc
- (void)viewWillAppear:(BOOL)animated{
    [super viewWillAppear:animated];
    NSLog(@"viewWillAppear");
}
```
Run起来看看输出吧！

我的理解：

- 方法交换对于我来说更像是实现一种思想的最佳技术：AOP面向切面编程。<br>
- 既然是切面，就一定不要忘记，交换完再调回自己。<br>
- 一定要保证只交换一次，否则就会很乱。<br>
- 最后，据说这个技术很危险，谨慎使用。<br>

