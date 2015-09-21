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

为什么这几个方法很有用呢？因为开发者可以通过它们<color =red>在分类中给已存在的类中添加自定义属性。</color>