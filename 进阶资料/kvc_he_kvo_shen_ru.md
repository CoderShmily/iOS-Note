# KVC概述

#### 1.什么是KVC？

KVC即NSKeyValueCoding,键/值编码,一个非正式的Protocol,以字符串的形式向对象发送消息，而不是通过调用存取方法，直接或通过实例变量访问的机制。

valueForKey:首先查找以键-key或-isKey命名的getter方法。如果不存在getter方法（假如我们没有通过@synthesize提供存取方法），它将在对象内部查找名为_key或key的实例变量。
对于KVC，Cocoa自动放入和取出标量值（int，float和struct）放入NSNumber或NSValue中；当使用-setValue:ForKey:时，它自动将标量值从这些对象中取出。仅KVC具有这种自动包装功能，常规方法调用和属性语法不具备该功能。

-setValue:ForKey:的工作方式和-valueForKey:相同。它首先查找名称的setter方法，如果不存在setter方法，它将在类中查找名为_key或key的实例变量。

#### 2.使用KVC说明
- KVC间接修改对象属性时，会自动判断对象属性的类型，完成相应的转换。
- KVC按键值路径取值时，如果对象不包含指定的键值，那么就会自动进入对象内部，查找对象属性。
- KVC可以嵌套按照键值路径取值。

值得注意的是这个不仅可以访问作为对象属性，而且也能访问一些标量（例如 int 和 CGFloat）和 struct（例如 CGRect）
```objc
[object setValue:@(20) forKey:@"height"];
```

#### 3.KVC和KVO有什么区别？
KVO是建立在KVC之上的，KVO能够观察一个对象的KVC key-path值的变化。

---


# KVO概述
#### 1.什么是KVO？
KVO是Key-Value Observing的缩写。KVO是Cocoa提供的一种称为键－值观察的机制，对象可以通过它得到其他对象特性属性的变更通知。这种机制在MVC模式的场景中很重要，因为它让视图对象可以经由控制器层观察模型对象的变更。

这一机制基于NSKeyValueObserving非正式协议，Cocoa通过这个协议为所有遵守协议的对象提供了一种自动化的属性观察能力。要实现自动观察，参与KVO的对象需要符合KVC的要求和存取方法，也可以手动实现观察者通知，也可以两者都保留。

#### 2.KVO的好处
- 使用KVO最直接的好处就是可以减少代码量。
- KVO是观察者设计模式中的一种，有利与业务逻辑于视图控制之间的解耦。



#### 3.更深刻的了解KVO，你可以动手写一个小demo思路如下：
- 定义一个对象People，分别有name和age属性
- 监听People的age属性
- 定义一个UIButton，在button的点击方法里面，去改变People的age。
- 你就可以收到age改变时发出来的通知
- 在对象销毁的时候，移除通知。

# KVO 进阶
KVO(Key Value Observing)，是观察者模式在Foundation中的实现

# KVO Compliance（KVO兼容）

有两种方法可以保证变更通知被发出。自动发送通知是NSObject提供的，并且一个类中的所有属性都默认支持，只要是符合KVO的。一般情况你使用自动变更通知，你不需要写任何代码。
人工变更通知需要些额外的代码，但也对通知发送提供了额外的控制。你可以通过重写子类automaticallyNotifiesObserversForKey:方法的方式控制子类一些属性的自动通知。
- Automatic Change Notification（自动通知）
    下面代码中的方法都能导致KVO变更消息发出

```objc
// Call the accessor method.
[account setName:@"Savings"];
 
// Use setValue:forKey:.
[account setValue:@"Savings" forKey:@"name"];
 
// Use a key path, where 'account' is a kvc-compliant property of 'document'.
[document setValue:@"Savings" forKeyPath:@"account.name"];
 
// Use mutableArrayValueForKey: to retrieve a relationship proxy object.
Transaction *newTransaction = <#Create a new transaction for the account#>;
NSMutableArray *transactions = [account mutableArrayValueForKey:@"transactions"];
[transactions addObject:newTransaction];
```
- Manual Change Notification（手动通知）

    下面的代码为openingBalance属性开启了人工通知，并让父类决定其他属性的通知方式。

```objc
+ (BOOL)automaticallyNotifiesObserversForKey:(NSString *)theKey {
 
    BOOL automatic = NO;
    if ([theKey isEqualToString:@"openingBalance"]) {
        automatic = NO;
    }
    else {
        automatic = [super automaticallyNotifiesObserversForKey:theKey];
    }
    return automatic;
}
```
要实现人工观察者通知，你要执行在变更前执行willChangeValueForKey:方法，在变更后执行didChangeValueForKey:方法：

```objc
- (void)setOpeningBalance:(double)theBalance {
    [self willChangeValueForKey:@"openingBalance"];
    _openingBalance = theBalance;
    [self didChangeValueForKey:@"openingBalance"];
}
```

为了使不必要的通知最小化我们应该在变更前先检查一下值是否变了：
```objc
- (void)setOpeningBalance:(double)theBalance {
    if (theBalance != _openingBalance) {
        [self willChangeValueForKey:@"openingBalance"];
        _openingBalance = theBalance;
        [self didChangeValueForKey:@"openingBalance"];
    }
}
```
如果一个操作导致了多个键的变化，你必须嵌套变更通知：

```objc
- (void)setOpeningBalance:(double)theBalance {
    [self willChangeValueForKey:@"openingBalance"];
    [self willChangeValueForKey:@"itemChanged"];
    _openingBalance = theBalance;
    _itemChanged = _itemChanged+1;
    [self didChangeValueForKey:@"itemChanged"];
    [self didChangeValueForKey:@"openingBalance"];
}
```

#### KVO的原理

简而言之就是：
- 当一个object有观察者时，动态创建这个object的类的子类
- 对于每个被观察的property，重写其set方法
- 在重写的set方法中调用- willChangeValueForKey:和- didChangeValueForKey:通知观察者
- 当一个property没有观察者时，删除重写的方法
- 当没有observer观察任何一个property时，删除动态创建的子类

空说无凭，简单验证下。
```objc
@interface Sark : NSObject
@property (nonatomic, copy) NSString *name;
@end

@implementation Sark
@end
Sark *sark = [Sark new];
// breakpoint 1
[sark addObserver:self forKeyPath:@"name" options:NSKeyValueObservingOptionNew context:nil];
// breakpoint 2
sark.name = @"萨萨萨";
[sark removeObserver:self forKeyPath:@"name"];
// breakpoint 3
```
断住后分别使用- class和object_getClass()打出sark对象的Class和真实的Class
```objc
// breakpoint 1
(lldb) po sark.class
Sark
(lldb) po object_getClass(sark)
Sark

// breakpoint 2
(lldb) po sark.class
Sark
(lldb) po object_getClass(sark)
NSKVONotifying_Sark

// breakpoint 3
(lldb) po sark.class
Sark
(lldb) po object_getClass(sark)
Sark
```
上面的结果说明，在sark对象被观察时,framework使用runtime动态创建了一个Sark类的子类`NSKVONotifying_Sark`而且为了隐藏这个行为,`NSKVONotifying_Sark`重写了-class方法返回之前的类,就好像什么也没发生过一样但是使用object_getClass()时就暴露了, 因为这个方法返回的是这个对象的`isa指针`，`这个指针指向的一定是个这个对象的类对象`.

然后来偷窥一下这个动态类实现的方法，这里请出一个NSObject的扩展NSObject+DLIntrospection，它封装了打印一个类的方法、属性、协议等常用调试方法，一目了然。
```objc
@interface NSObject (DLIntrospection)
+ (NSArray *)classes;
+ (NSArray *)properties;
+ (NSArray *)instanceVariables;
+ (NSArray *)classMethods;
+ (NSArray *)instanceMethods;

+ (NSArray *)protocols;
+ (NSDictionary *)descriptionForProtocol:(Protocol *)proto;

+ (NSString *)parentClassHierarchy;
@end
```
然后继续在刚才的断点处调试：
```
// breakpoint 1
(lldb) po [object_getClass(sark) instanceMethods]
<__NSArrayI 0x8e9aa00>(
- (void)setName:(id)arg0 ,
- (void).cxx_destruct,
- (id)name
)
// breakpoint 2
(lldb) po [object_getClass(sark) instanceMethods]
<__NSArrayI 0x8d55870>(
- (void)setName:(id)arg0 ,
- (class)class,
- (void)dealloc,
- (BOOL)_isKVOA
)
// breakpoint 3
(lldb) po [object_getClass(sark) instanceMethods]
<__NSArrayI 0x8e9cff0>(
- (void)setName:(id)arg0 ,
- (void).cxx_destruct,
- (id)name
)
```
首先就有个扎眼的`- .cxx_destruct`冒出来，这货是个啥？详细的探 究请参考我的另一篇文章。

大概就是说arc下这个方法在所有dealloc调用完成后负责释放所有的变量，当然这个和kvo没啥关系了，回到正题。
从上面breakpoint2的打印可以看出，动态类重写了4个方法：

- `- setName:`最主要的重写方法，set值时调用通知函数
- `- class`隐藏自己必备啊，返回原来类的class
- `- dealloc`做清理犯罪现场工作
- `- _isKVOA`这就是内部使用的标示了，判断这个类有没被KVO动态生成子类

接下来验证一下KVO重写set方法后是否调用了`- willChangeValueForKey:`和<br>`- didChangeValueForKey:`最直接的验证方法就是在Sark类中重写这两个方法：

```objc
@implementation Sark

- (void)willChangeValueForKey:(NSString *)key
{
    NSLog(@"%@", NSStringFromSelector(_cmd));
    [super willChangeValueForKey:key];
}

- (void)didChangeValueForKey:(NSString *)key
{
    NSLog(@"%@", NSStringFromSelector(_cmd));
    [super didChangeValueForKey:key];
}

@end
```
没问题。
