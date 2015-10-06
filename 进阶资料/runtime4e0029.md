###Objective-C
Objective-C 扩展了 C 语言，并加入了面向对象特性和 Smalltalk 式的消息传递机制。而这个扩展的核心是一个用 C 和 编译语言 写的 Runtime 库。它是 Objective-C 面向对象和动态机制的基石.

Objective-C 是一个动态语言，这意味着它不仅需要一个编译器，也需要一个运行时系统来动态得创建类和对象、进行消息传递和转发。理解 Objective-C 的 Runtime 机制可以帮我们更好的了解这个语言，适当的时候还能对语言进行扩展，从系统层面解决项目中的一些设计或技术问题。了解 Runtime ，要先了解它的核心 - `消息传递`（Messaging）。

---

### 消息传递

在很多语言，比如 C ，调用一个方法其实就是跳到内存中的某一点并开始执行一段代码。没有任何动态的特性，因为这在编译时就决定好了。而在 Objective-C 中，[object foo] 语法并不会立即执行 foo 这个方法的代码。它是在运行时给 object 发送一条叫 foo 的消息。这个消息，也许会由 object 来处理，也许会被转发给另一个对象，或者不予理睬假装没收到这个消息。多条不同的消息也可以对应同一个方法实现。这些都是在程序运行的时候决定的。

事实上，在编译时你写的 Objective-C 函数调用的语法都会被翻译成一个 C 的函数调用` - objc_msgSend()` 。比如，下面两行代码就是等价的：

```objc
[array insertObject:foo atIndex:5];

objc_msgSend(array, @selector(insertObject:atIndex:), foo, 5);
```
消息传递的关键藏于 `objc_object` 中的 isa 指针和 `objc_class` 中的 class dispatch table。

## `objc_object, objc_class `以及` Ojbc_method`
在 Objective-C 中，类、对象和方法都是一个 C 的结构体，从 objc/objc.h 头文件中，我们可以找到他们的定义：

```objc
struct objc_object {  
    Class isa  OBJC_ISA_AVAILABILITY;
};

struct objc_class {  
    Class isa  OBJC_ISA_AVAILABILITY;
#if !__OBJC2__
    Class super_class;
    const char *name;
    long version;
    long info;
    long instance_size;
    struct objc_ivar_list *ivars;
    **struct objc_method_list **methodLists**;
    **struct objc_cache *cache**;
    struct objc_protocol_list *protocols;
#endif
};

struct objc_method_list {  
    struct objc_method_list *obsolete;
    int method_count;

#ifdef __LP64__
    int space;
#endif

    /* variable length structure */
    struct objc_method method_list[1];
};

struct objc_method {  
    SEL method_name;
    char *method_types;    /* a string representing argument/return types */
    IMP method_imp;
};
```
`objc_method_list` 本质是一个有 `objc_method` 元素的可变长度的数组。一个 `objc_method` 结构体中有函数名，也就是SEL，有表示函数类型的字符串 (见 Type Encoding) ，以及函数的实现IMP。

从这些定义中可以看出发送一条消息也就 `objc_msgSend` 做了什么事。举 `objc_msgSend(obj, foo)` 这个例子来说：

1.首先，通过 obj 的 isa 指针找到它的 class ;

2.在 class 的 method list 找 foo ;

3.如果 class 中没到 foo，继续往它的 superclass 中找 ;

4.一旦找到 foo 这个函数，就去执行它的实现IMP .

但这种实现有个问题，效率低。但一个 class 往往只有 20% 的函数会被经常调用，可能占总调用次数的 80% 。每个消息都需要遍历一次 `objc_method_list` 并不合理。如果把经常被调用的函数缓存下来，那可以大大提高函数查询的效率。这也就是 `objc_class` 中另一个重要成员 `objc_cache` 做的事情 - 再找到 foo 之后，把 foo 的 `method_name` 作为 key ，`method_imp` 作为 value 给存起来。当再次收到 foo 消息的时候，可以直接在 cache 里找到，避免去遍历 `objc_method_list`.