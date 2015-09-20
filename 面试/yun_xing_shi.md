```objc
    class_getInstanceMethod();
    class_addMethod();
    
    method_getImplementation();
    method_getTypeEncoding();
    method_exchangeImplementations;

```

```objc
// 通过class_addMethod方法将一个函数加入到类的方法中
class_addMethod(__unsafe_unretained Class cls, SEL name, IMP imp, const char *types);


```

```objc
     object_getClass(id obj);
     object_getClassName(id obj);
     object_getIvar(id obj, Ivar ivar);
     object_isClass(id obj)
     
     object_setClass(id obj, __unsafe_unretained Class cls)
     object_setIvar(id obj, Ivar ivar, id value)
```