# 方法与消息


### 基础数据类型
---
###**SEL**

SEL又叫选择器，是表示一个方法的selector的指针，其定义如下：
```objc
typedef struct objc_selector *SEL;
```
Objective-C在编译时，会依据每一个方法的名字、参数序列，生成一个唯一的整型标识(Int类型的地址)，这个标识就是SEL。

两个类之间，不管它们是父类与子类的关系，还是之间没有这种关系，只要方法名相同，那么方法的SEL就是一样的。每一个方法都对应着一个SEL。所以在Objective-C同一个类(及类的继承体系)中，不能存在2个同名的方法，即使参数类型不同也不行。相同的方法只能对应一个SEL。这也就导致Objective-C在处理相同方法名且参数个数相同但类型不同的方法方面的能力很差。如在某个类中定义以下两个方法：
```objc
- (void)setWidth:(int)width;

- (void)setWidth:(double)width;
```

这样的定义被认为是一种编译错误，所以我们不能像C++, C#那样。

当然，不同的类可以拥有相同的selector，这个没有问题。不同类的实例对象执行相同的selector时，会在各自的方法列表中去根据selector去寻找自己对应的IMP。

本质上，SEL只是一个指向方法的指针（准确的说，只是一个根据方法名hash化了的KEY值，能唯一代表一个方法），它的存在只是为了加快方法的查询速度。这个查找过程我们将在下面讨论。

我们可以在运行时添加新的selector，也可以在运行时获取已存在的selector，我们可以通过下面三种方法来获取SEL:

- sel_registerName函数
- Objective-C编译器提供的@selector()
- NSSelectorFromString()方法

---
**IMP**

IMP实际上是一个函数指针，指向方法实现的首地址.
```objc
id (*IMP)(id, SEL, ...)
```
这个函数使用当前CPU架构实现的标准的C调用约定。第一个参数是指向self的指针(如果是实例方法，则是类实例的内存地址；如果是类方法，则是指向元类的指针)，第二个参数是方法选择器(selector)，接下来是方法的实际参数列表。

前面介绍过的SEL就是为了查找方法的最终实现IMP的。由于每个方法对应唯一的SEL，因此我们可以通过SEL方便快速准确地获得它所对应的IMP，查找过程将在下面讨论。取得IMP后，我们就获得了执行这个方法代码的入口点，此时，我们就可以像调用普通的C语言函数一样来使用这个函数指针了。

通过取得IMP，我们可以跳过Runtime的消息传递机制，直接执行IMP指向的函数实现，这样省去了Runtime消息传递过程中所做的一系列查找操作，会比直接向对象发送消息高效一些。

---
###**Method**
介绍完SEL和IMP，我们就可以来讲讲Method了。Method用于表示类定义中的方法，则定义如下：
```objc
typedef struct objc_method *Method;



struct objc_method {

    SEL method_name                 OBJC2_UNAVAILABLE;  // 方法名

    char *method_types                  OBJC2_UNAVAILABLE;

    IMP method_imp                      OBJC2_UNAVAILABLE;  // 方法实现

} 
```
我们可以看到该结构体中包含一个SEL和IMP，实际上相当于在SEL和IMP之间作了一个映射。有了SEL，我们便可以找到对应的IMP，从而调用方法的实现代码。具体操作流程我们将在下面讨论。
`objc_method_description`

objc_method_description定义了一个Objective-C方法，其定义如下：
```objc
struct objc_method_description { SEL name; char *types; };
```
---
### **方法相关操作函数**

Runtime提供了一系列的方法来处理与方法相关的操作。包括方法本身及SEL。本节我们介绍一下这些函数。

`方法`

方法操作相关函数包括下以：
```objc
id method_invoke ( id receiver, Method m, ... );

// 调用返回一个数据结构的方法的实现
void method_invoke_stret ( id receiver, Method m, ... );

// 获取方法名
SEL method_getName ( Method m );

// 返回方法的实现
IMP method_getImplementation ( Method m );

// 获取描述方法参数和返回值类型的字符串
const char * method_getTypeEncoding ( Method m );

// 获取方法的返回值类型的字符串
char * method_copyReturnType ( Method m );

// 获取方法的指定位置参数的类型字符串
char * method_copyArgumentType ( Method m, unsigned int index );

// 通过引用返回方法的返回值类型字符串
void method_getReturnType ( Method m, char *dst, size_t dst_len );

// 返回方法的参数的个数
unsigned int method_getNumberOfArguments ( Method m );

// 通过引用返回方法指定位置参数的类型字符串
void method_getArgumentType ( Method m, unsigned int index, char *dst, size_t dst_len );

// 返回指定方法的方法描述结构体
struct objc_method_description * method_getDescription ( Method m );

// 设置方法的实现
IMP method_setImplementation ( Method m, IMP imp );

// 交换两个方法的实现
void method_exchangeImplementations ( Method m1, Method m2 );
```
- method_invoke函数，返回的是实际实现的返回值。参数receiver不能为空。这个方法的效率会比method_getImplementation和method_getName更快。

- method_getName函数，返回的是一个SEL。如果想获取方法名的C字符串，可以使用sel_getName(method_getName(method))。

- method_getReturnType函数，类型字符串会被拷贝到dst中。

- method_setImplementation函数，注意该函数返回值是方法之前的实现。
---
### 方法选择器
选择器相关的操作函数包括：
```objc
// 返回给定选择器指定的方法的名称
const char * sel_getName ( SEL sel );

// 在Objective-C Runtime系统中注册一个方法，将方法名映射到一个选择器，并返回这个选择器
SEL sel_registerName ( const char *str );

// 在Objective-C Runtime系统中注册一个方法
SEL sel_getUid ( const char *str );

// 比较两个选择器
BOOL sel_isEqual ( SEL lhs, SEL rhs );
```
- sel_registerName函数：在我们将一个方法添加到类定义时，我们必须在Objective-C Runtime系统中注册一个方法名以获取方法的选择器。