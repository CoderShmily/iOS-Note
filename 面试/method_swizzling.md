### Method Swizzling功能:
允许我们动态地替换方法的实现，实现 Hook 功能，是一种比子类化更加灵活的“重写”方法的方式。


原则上，方法的名称 name 和方法的实现 imp 是一一对应的，而 Method Swizzling 的原理就是动态地改变它们的对应关系，以达到替换方法实现的目的。

使用 Method Swizzling 

- 第 1 种情况：主类本身有实现需要替换的方法，也就是 class_addMethod 方法返回 NO 。这种情况的处理比较简单，直接交换两个方法的实现就可以了.
- 第 2 种情况：主类本身没有实现需要替换的方法，而是继承了父类的实现，即 class_addMethod 方法返回 YES 。
`
这时使用 class_getInstanceMethod 函数获取到的 originalSelector 指向的就是父类的方法，我们再通过执行 class_replaceMethod(class, swizzledSelector, method_getImplementation(originalMethod), method_getTypeEncoding(originalMethod)); 将父类的实现替换到我们自定义的 mrc_viewWillAppear 方法中。这样就达到了在 mrc_viewWillAppear 方法的实现中调用父类实现的目的。
`

下面我们就以替换 viewWillAppear 方法为例谈谈 Method Swizzling 的最佳实践
```
@interface UIViewController (MRCUMAnalytics)

@end

@implementation UIViewController (MRCUMAnalytics)

+ (void)load {
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        Class class = [self class];

        SEL originalSelector = @selector(viewWillAppear:);
        SEL swizzledSelector = @selector(mrc_viewWillAppear:);

        Method originalMethod = class_getInstanceMethod(class, originalSelector);
        Method swizzledMethod = class_getInstanceMethod(class, swizzledSelector);

        BOOL success = class_addMethod(class, originalSelector, method_getImplementation(swizzledMethod), method_getTypeEncoding(swizzledMethod));
        if (success) {
            class_replaceMethod(class, swizzledSelector, method_getImplementation(originalMethod), method_getTypeEncoding(originalMethod));
        } else {
            method_exchangeImplementations(originalMethod, swizzledMethod);
        }
    });
}

#pragma mark - Method Swizzling

- (void)mrc_viewWillAppear:(BOOL)animated {
    [self mrc_viewWillAppear:animated];
    [MobClick beginLogPageView:NSStringFromClass([self class])];
}

@end
```
