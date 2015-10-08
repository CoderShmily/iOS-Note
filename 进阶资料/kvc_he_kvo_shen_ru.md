# KVC概述

#### 1.什么是KVC？
KVC即NSKeyValueCoding，就是键-值编码的意思。一个非正式的 Protocol，是一种间接访问对象的属性使用字符串来标识属性，而不是通过调用存取方法，直接或通过实例变量访问的机制。

键/值编码中的基本调用包括-valueForKey:和-setValue:forKey:。以字符串的形式向对象发送消息，这个字符串是我们关注的属性的关键。
valueForKey:首先查找以键-key或-isKey命名的getter方法。如果不存在getter方法（假如我们没有通过@synthesize提供存取方法），它将在对象内部查找名为_key或key的实例变量。
对于KVC，Cocoa自动放入和取出标量值（int，float和struct）放入NSNumber或NSValue中；当使用-setValue:ForKey:时，它自动将标量值从这些对象中取出。仅KVC具有这种自动包装功能，常规方法调用和属性语法不具备该功能。
-setValue:ForKey:的工作方式和-valueForKey:相同。它首先查找名称的setter方法，如果不存在setter方法，它将在类中查找名为_key或key的实例变量。

#### 2.使用KVC说明
- KVC间接修改对象属性时，会自动判断对象属性的类型，完成相应的转换。
- KVC按键值路径取值时，如果对象不包含指定的键值，那么就会自动进入对象内部，查找对象属性。
- KVC可以嵌套按照键值路径取值。

#### 3.KVC和KVO有什么区别？
KVO是建立在KVC之上的，KVO能够观察一个对象的KVC key-path值的变化。

---


# KVO概述
#### 1.什么是KVO？
KVO是Key-Value Observing的缩写。它提供一种机制，当指定的对象的属性被修改后，则对象就会接受到通知。简单的说就是每次指定的被观察的对象的属性被修改后，KVO就会自动通知相应的观察者了。

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
上面的结果说明，在sark对象被观察时,framework使用runtime动态创建了一个Sark类的子类`NSKVONotifying_Sark`而且为了隐藏这个行为,`NSKVONotifying_Sark`重写了-class方法返回之前的类,就好像什么也没发生过一样但是使用object_getClass()时就暴露了, 因为这 个方法返回的是这个对象的`isa指针`，`这个指针指向的一定是个这个对象的类对象`.

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
