### Associated Objects
```
#import <objc/runtime.h>
```
Objective-C开发者在遇到上面这条“咒语”相关的一些东西时，会不自觉的变的非常谨慎。一个主要原因是：弄乱Objective-C运行时可能会改变整个实现结构，因为所有的代码都是运行在它之上的。

一方面：<objc/runtime.h>中的函数可以给应用或者框架增加强大的新特性，这是通过其他方式不可能做到的。但另一方面：它会改变代码的正常运行逻辑和所有与之交互的东西（通常伴随着可怕的副作用）。

因而，这是我们认为进行这种魔鬼交易最大的恐惧点，下面来看一个NSHipster读者问得最多的一个主题：associated objects。

----

Associated Objects（关联对象）或者叫作关联引用（Associative References），是作为Objective-C 2.0 运行时功能被引入到 Mac OS X 10.6 Snow Leopard（及iOS4）系统。与它相关在<objc/runtime.h>中有3个C函数，它们可以让对象在运行时关联任何值：
- objc_setAssociatedObject
- objc_getAssociatedObject
- objc_removeAssociatedObjects

为什么这几个方法很有用呢？因为开发者可以通过它们**在分类中给已存在的类中添加自定义属性。**

#### NSObject+AssociatedObject.h
```
@interface NSObject (AssociatedObject)
@property (nonatomic, strong) id associatedObject;
@end
```
#### NSObject+AssociatedObject.m
```
@implementation NSObject (AssociatedObject)
@dynamic associatedObject;

- (void)setAssociatedObject:(id)object {
     objc_setAssociatedObject(self, @selector(associatedObject), object, OBJC_ASSOCIATION_RETAIN_NONATOMIC);
}

- (id)associatedObject {
    return objc_getAssociatedObject(self, @selector(associatedObject));
}
```
通常推荐key使用static char类型——使用指针或许更好，key值是一个唯一的常量，并只在getters和setters方法内部使用：
```
static char kAssociatedObjectKey;

objc_getAssociatedObject(self, &kAssociatedObjectKey);
```
然而，一个更简单的方案是：直接使用选择器（selector）。
```
因为SEL生成的时候就是一个唯一的常量，你可以使用 _cmd 作为objc_setAssociatedObject()的key。
```
#### 关联对象的特性
---
被关联到对象的值根据使用的objc_AssociationPolicy类型不同表现出不同的特性：

| Behavior | 对应的@property类型 | 描述|
| -- | -- | -- |
| OBJC_ASSOCIATION_ASSIGN | @property (assign) 或 @property(unsafe_unretained)| 给关联对象指定若引用 |
| OBJC_ASSOCIATION_RETAIN_NONATOMIC | @property (nonatomic, strong) | 给关联对象指定非原子的强引用 |
| OBJC_ASSOCIATION_COPY_NONATOMIC | @property (nonatomic, copy) | 给关联对象指定非原子的copy特性 |
| OBJC_ASSOCIATION_RETAIN| @property (atomic, strong) | 给关联对象指定原子的强引用 |
| OBJC_ASSOCIATION_COPY | @property (atomic, copy) |给关联对象指定原子copy特性 |
通过OBJC_ASSOCIATION_ASSIGN分配的弱关联对象并不是完全和weak修饰符引用一样（对象初始化与释放时被置空），反而更像是unsafe_unretained，所以你需要在访问弱关联对象时稍微注意一下。
```
根据WWDC2011,Session322对对象释放时间的描述，associated objects清除在对象生命周期中很晚才执行，通过被NSObject -dealloc方法调用的object_dispose()函数完成。
```
#### 移除关联对象
---
一个的方法是试图在某个时刻调用objc_removeAssociatedObjects()函数来移除关联对象，然而，根据苹果文档描述，你不大可能有需求要自己去调用：
```
这个函数的主要目的是很容易的让对象恢复成它“原始状态”，你不应该使用它来移除关联的对象，因为它也会移除掉包括其他地方加入的全部的关联对象。所以一般你只需要通过调用objc_setAssociatedObject并传入nil值来清除关联值。
```

#### 模式
---
- **添加私有变量来帮助实现细节 。** 当拓展一个内置类时，可能有必要跟踪一些额外的状态，这是关联对象最普遍的应用场景。例如：AFNetworking中在UIImageView的分类中使用关联对象来存储一个请求操作对象（operation object），用于异步的从远程获取图片。
- **添加公共属性来设置分类的特性。**有时候，通过添加一个属性让一个分类更加灵活，而不是作为函数参数。这种情况下，使用关联对象作为一个公开的属性是可接受的解决方案。还是拿前面AFNetworking的例子来说，UIImageView的分类中imageResponseSerializer属性允许图片视图随意的使用一个过滤器，或者在图片请求并缓存之前就可以修改它的渲染。
- **为KVO创建一个关联的观察者（observer）。**当在一个分类中使用KVO的时候，推荐使用一个自定义的关联对象作为观察者，而不是对象自己观察自己。

#### 反模式
---
- **在不必要的时候使用关联对象**。使用视图时一个常见的情况是通过数据模型或一些复合的值来创建一个便利的方法设置填充字段或属性。如果这些值在后面不会再被使用到，最好就不要使用关联对象了。

- **使用关联对象来保存一个可以被推算出来的值。**例如，有人可能想通过关联对象存储UITableViewCell上一个自定义accessoryView的引用，使用tableView:accessoryButtonTappedForRowWithIndexPath: 和 cellForRowAtIndexPath:即可以达到要求。

- **使用关联对象来代替X。**其中X代表下面的一些项：
    - 子类化，当使用继承比使用组合更合适的时候。
    - Target-Action给响应者添加交互事件。
    - 手势识别，当target-action模式不够用的时候。
    - 代理，当事件可以委托给其他对象。
    - 消息 & 消息中心使用低耦合的方式来广播消息。

---
关联对象应该被当做最后的手段来使用（不得不用时才用），而不是为了寻求一个解决方案就行（事实上，分类本身就不应该是解决问题优先选择的工具）。

像一些巧妙的伎俩、hack手段或者是变通的解决方案，人们总是倾向于创造机会来使用他们——特别是刚刚接触他们时。尽可能的在理解并领悟之后再做出正确的方案，避免自己陷入一知半解的尴尬处境。
