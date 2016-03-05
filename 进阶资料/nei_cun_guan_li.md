# 内存管理

### 1.什么是ARC？

ARC是automatic reference counting自动引用计数，在程序编译时自动加入retain/release。在对象被创建时retain count+1，在对象被release时count-1，当count=0时，销毁对象。程序中加入autoreleasepool对象会由系统自动加上autorelease方法，如果该对象引用计数为0，则销毁。那么ARC是为了解决MRC手动管理内存存在的一些而诞生的。

MRC下内存管理的缺点：

- 释放一个堆内存时，首先要确定指向这个堆空间的指针都被release了。(避免提前释放)
- 释放指针指向的堆空间，首先要确定哪些指向同一个堆，这些指针只能释放一次。(避免释放多次，造成内存泄露)
- 模块化操作时，对象可能被多个模块创建和使用，不能确定最后由谁释放
- 多线程操作时，不确定哪个线程最后使用完毕。

虽然ARC给我们编程带来的很多好多，但也可能出现内存泄露。如下面两种情况：

- 循环引用：A有个属性强引用B，B有个属性强引用A，两个对象都无法释放。
- 死循环：如果有个ViewController中有无限循环，也会导致即使ViewController对应的view消失了，ViewController也不能释放。

### 2.block一般用那个关键字修饰，为什么？

block一般使用copy关键之进行修饰，block使用copy是从MRC遗留下来的“传统”，在MRC中，方法内容的block是在栈区的，使用copy可以把它放到堆区。但在ARC中写不写都行：编译器自动对block进行了copy操作。

### 3.用@property声明的NSString（或NSArray，NSDictionary）经常使用copy关键字，为什么？如果改用strong关键字，可能造成什么问题？

答：用@property声明 NSString、NSArray、NSDictionary 经常使用copy关键字，是因为他们有对应的可变类型：NSMutableString、NSMutableArray、NSMutableDictionary，他们之间可能进行赋值操作，为确保对象中的字符串值不会无意间变动，应该在设置新属性值时拷贝一份。

如果我们使用是strong,那么这个属性就有可能指向一个可变对象,如果这个可变对象在外部被修改了,那么会影响该属性。

copy此特质所表达的所属关系与strong类似。然而设置方法并不保留新值，而是将其“拷贝” (copy)。 当属性类型为NSString时，经常用此特质来保护其封装性，因为传递给设置方法的新值有可能指向一个NSMutableString类的实例。这个类是NSString的子类，表示一种可修改其值的字符串，此时若是不拷贝字符串，那么设置完属性之后，字符串的值就可能会在对象不知情的情况下遭人更改。所以，这时就要拷贝一份“不可变” (immutable)的字符串，确保对象中的字符串值不会无意间变动。只要实现属性所用的对象是“可变的” (mutable)，就应该在设置新属性值时拷贝一份。

### 4.runloop、autorelease pool以及线程之间的关系。

每个线程(包含主线程)都有一个Runloop。对于每一个Runloop，系统会隐式创建一个Autorelease pool，这样所有的release pool会构成一个像callstack一样的一个栈式结构，在每一个Runloop结束时，当前栈顶的Autorelease pool会被销毁，这样这个pool里的每个Object会被release。

### 5.@property 的本质是什么？ivar、getter、setter 是如何生成并添加到这个类中的。

“属性”(property)有两大概念：ivar(实例变量)、存取方法(access method=getter)，即@property = ivar + getter + setter。
以前是@property @synthesize配合使用，现在用@property替代

### 6.分别写一个setter方法用于完成@property (nonatomic,retain)NSString *name和@property (nonatomic,copy) NSString *name

retain属性的setter方法是保留新值并释放旧值，然后更新实例变量，令其指向新值。顺序很重要。假如还未保留新值就先把旧值释放了，而且两个值又指向同一个对象，先执行的release操作就可能导致系统将此对象永久回收。
```objc
-(void)setName:(NSString *)name
{
    [name retain];
    [_name release];
    _name = name;
}
-(void)setName:(NSString *)name
{
     
    [_name release];
    _name = [name copy];
}
```
### 7.说说assign vs weak，_block vs _weak的区别

assign适用于基本数据类型，weak是适用于NSObject对象，并且是一个弱引用。

assign其实页可以用来修饰对象，那么为什么不用它呢？因为被assign修饰的对象在释放之后，指针的地址还是存在的，也就是说指针并没有被置为nil。如果在后续内存分配中，刚才分到了这块地址，程序就会崩溃掉。而weak修饰的对象在释放之后，指针地址会被置为nil。

- _block是用来修饰一个变量，这个变量就可以在block中被修改。
- _block:使用_block修饰的变量在block代码块中会被retain(ARC下，MRC下不会retain)
- _weak:使用_weak修饰的变量不会在block代码块中被retain

### 8.请说出下面代码是否有问题，如果有问题请修改？
```objc
@autoreleasepool {
        for (int i = 0; i < largeNumber; i++) {
            Person *per = [[Person alloc] init];
            [per autorelease];
        }
    }
```
内存管理的原则：如果对一个对象使用了alloc、copy、retain，那么你必须使用相应的release或者autorelease。咋一看，这道题目有alloc，也有autorelease，两者对应起来，应该没问题。但autorelease虽然会使引用计数减一，但是它并不是立即减一，它的本质功能只是把对象放到离他最近的自动释放池里。当自动释放池销毁了，才会向自动释放池中的每一个对象发送release消息。这道题的问题就在autorelease。因为largeNumber是一个很大的数，autorelease又不能使引用计数立即减一，所以在循环结束前会造成内存溢出的问题。

解决方案如下：
```objc
@autoreleasepool {
        for (int i = 0; i < largeNumber; i++) { 
            @autoreleasepool {
            Person *per = [[Person alloc] init];
            [per autorelease];
        }
      }
    }
```
在循环内部再加一个自动释放池，这样就能保证每创建一个对象就能及时释放。

### 9.请问下面代码是否有问题，如有问题请修改？
```objc
@autoreleasepool {
        NSString *str = [[NSString alloc] init];
        [str retain];
        [str retain];
        str = @"jxl";
        [str release];
        [str release];
        [str release];
}
```
这道题跟第8题一样存在内存泄露问题，1.内存泄露 2.指向常量区的对象不能release。

指针变量str原本指向一块开辟的堆区空间，但是经过重新给str赋值，str的指向发生了变化，由原来指向堆区空间，到指向常量区。常量区的变量根本不需要释放，这就导致了原来开辟的堆区空间没有释放，照成内存泄露。

### 10.什么情况下使用weak关键字，相比assign有什么不同？什么情况使用weak关键字？
- 在ARC中，在有可能出现循环引用的时候，往往要通过让其中一端使用weak来解决。比如delegate代理
- 自身已经对它进行一次强引用，没有必要再强引用一次，此时也会使用weak，自定义控件属性一般也使用weak。

不同点：
- weak此特质表明该属性定义了一种“非拥有关系”。为这种属性设置新值时，设置方法既不保留新值，也不释放旧值。此特性与assign一样，然而在属性所指的对象遭到推毁时，属性值也会清空。而assign的“设置方法”只会执行针对“纯量类型” (scalar type，例如 CGFloat 或 NSlnteger 等)的简单赋值操作。
- assign可以用非OC对象，而weak必须用于OC对象。

### 11.内存管理语义(assign、strong、weak等的区别)
- assign “设置方法” 只会执行针对“纯量”的简单赋值操作。
- strong  此特质表明该属性定义了一种“拥有关系”。为这种属性设置新值时，设置方法会先保留新值，并释放旧值，然后再将新值设置上去。
- weak 此特质表明该属性定义了一种“非拥有关系”。为这种属性设置新值时，设置方法既不保留新值，也不释放旧值。此特质同assign类似，然而在属性所指的对象遭到推毁时，属性值也会清空。
- unsafe_unretained  此特质的语义和assign相同，但是它适用于“对象类型”，该特质表达一种“非拥有关系”，当目标对象遭到推毁时，属性值不会自动清空，这一点与weak有区别。
- copy 此特质所表达的所属关系与strong类似。然而设置方法并不保留新值，而是设置方法并不保留新值，而是将其“拷贝”。当属性类型为NSString*时，经常用此特质来保护其封装性，因为传递给设置方法的新值有可能指向一个NSMutableString类的实例。这个类是- - NSString的子类，表示一种可以修改其值的字符串，此时若是不拷贝字符串，那么设置完属性之后，字符串的值就可能会在对象不知情的情况下遭人更改。所以，这时就要拷贝一份“不可变”的字符串，确保对象中的字符串值不会无意间变动。只要实现属性所用的对象是“可变的”，就应该在设置新属性值时拷贝一份。

