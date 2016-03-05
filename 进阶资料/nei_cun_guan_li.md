# 内存管理

1.什么是ARC？

ARC是automatic reference counting自动引用计数，在程序编译时自动加入retain/release。在对象被创建时retain count+1，在对象被release时count-1，当count=0时，销毁对象。程序中加入autoreleasepool对象会由系统自动加上autorelease方法，如果该对象引用计数为0，则销毁。那么ARC是为了解决MRC手动管理内存存在的一些而诞生的。

MRC下内存管理的缺点：

- 释放一个堆内存时，首先要确定指向这个堆空间的指针都被release了。(避免提前释放)
- 释放指针指向的堆空间，首先要确定哪些指向同一个堆，这些指针只能释放一次。(避免释放多次，造成内存泄露)
- 模块化操作时，对象可能被多个模块创建和使用，不能确定最后由谁释放
- 多线程操作时，不确定哪个线程最后使用完毕。

虽然ARC给我们编程带来的很多好多，但也可能出现内存泄露。如下面两种情况：

- 循环参照：A有个属性参照B，B有个属性参照A，如果都是strong参照的话，两个对象都无法释放。
- 死循环：如果有个ViewController中有无限循环，也会导致即使ViewController对应的view消失了，ViewController也不能释放。