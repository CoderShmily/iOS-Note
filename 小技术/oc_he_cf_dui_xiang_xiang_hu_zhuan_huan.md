iOS允许Objective-C 和 Core Foundation 对象之间可以轻松的转换，拿 NSString 和 CFStringRef 来说，直接转换豪无压力：

```objc
CFStringRef aCFString = (CFStringRef)aNSString;

NSString *aNSString = (NSString *)aCFString;
```
针对内存管理问题，ARC 可以帮忙管理 Objective-C 对象, 但是不支持 Core Foundation 对象的管理，所以转换后要注意一个问题：谁来释放使用后的对象。 本文重点总结一下类型转换后的内存管理。

一、非ARC的内存管理

倘若不使用ARC，手动管理内存，思路比较清晰，使用完，release转换后的对象即可。
```objc
//NSString 转 CFStringRef  
CFStringRef aCFString = (CFStringRef) [[NSString alloc] initWithFormat:@"%@", string];  
//...  
CFRelease(aCFString);  
  
  
//CFStringRef 转 NSString  
CFStringRef aCFString = CFStringCreateWithCString(kCFAllocatorDefault,  
                                                  bytes,  
                                                  NSUTF8StringEncoding);  
NSString *aNSString = (NSString *)aCFString;  
//...  
[aNSString release]; 
```
二、ARC下的内存管理

ARC的诞生大大简化了我们针对内存管理的开发工作，但是只支持管理 Objective-C 对象, 不支持 Core Foundation 对象。Core Foundation 对象必须使用CFRetain和CFRelease来进行内存管理。那么当使用Objective-C 和 Core Foundation 对象相互转换的时候，必须让编译器知道，到底由谁来负责释放对象，是否交给ARC处理。只有正确的处理，才能避免内存泄漏和double free导致程序崩溃。

根据不同需求，有3种转换方式
```objc
__bridge                        （不改变对象所有权）
__bridge_retained 或者 CFBridgingRetain()               （解除 ARC 所有权）
__bridge_transfer 或者 CFBridgingRelease()             （给予 ARC 所有权）
```
1. __bridge_retained 或者 CFBridgingRetain()

__bridge_retained 或者 CFBridgingRetain()  将Objective-C对象转换为Core Foundation对象，把对象所有权桥接给Core Foundation对象，同时剥夺ARC的管理权，后续需要开发者使用CFRelease或者相关方法手动来释放对象。

来看个例子：
```objc
- (void)viewDidLoad  
{  
    [super viewDidLoad];  
      
    NSString *aNSString = [[NSString alloc]initWithFormat:@"test"];  
    CFStringRef aCFString = (__bridge_retained CFStringRef) aNSString;  
      
    (void)aCFString;  
      
    //正确的做法应该执行CFRelease  
    //CFRelease(aCFString);    
} 
```
程序没有执行CFRelease，造成内存泄漏：



CFBridgingRetain()  是 __bridge_retained 的宏方法，下面两行代码等价：
```objc
CFStringRef aCFString = (__bridge_retained CFStringRef) aNSString;  
CFStringRef aCFString = (CFStringRef) CFBridgingRetain(aNSString); 
```
2. __bridge_transfer 或者 CFBridgingRelease()

__bridge_transfer 或者 CFBridgingRelease()  将非Objective-C对象转换为Objective-C对象，同时将对象的管理权交给ARC，开发者无需手动管理内存。

接着上面那个内存泄漏的例子，再转成OC对象交给ARC来管理内存，无需手动管理，也不会出现内存泄漏：
```objc
- (void)viewDidLoad  
{  
    [super viewDidLoad];  
      
    NSString *aNSString = [[NSString alloc]initWithFormat:@"test"];  
    CFStringRef aCFString = (__bridge_retained CFStringRef) aNSString;  
    aNSString = (__bridge_transfer NSString *)aCFString;  
} 
```
CFBridgingRelease() 是__bridge_transfer的宏方法，下面两行代码等价：
```objc
aNSString = (__bridge_transfer NSString *)aCFString;
aNSString = (NSString *)CFBridgingRelease(aCFString);
```
3. __bridge

__bridge 只做类型转换，不改变对象所有权，是我们最常用的转换符。

从OC转CF，ARC管理内存：
```objc
- (void)viewDidLoad  
{  
    [super viewDidLoad];  
      
    NSString *aNSString = [[NSString alloc]initWithFormat:@"test"];  
    CFStringRef aCFString = (__bridge CFStringRef)aNSString;  
      
    (void)aCFString;  
} 
```
从CF转OC，需要开发者手动释放，不归ARC管：
```objc
- (void)viewDidLoad  
{  
    [super viewDidLoad];  
      
    CFStringRef aCFString = CFStringCreateWithCString(NULL, "test", kCFStringEncodingASCII);  
    NSString *aNSString = (__bridge NSString *)aCFString;  
      
    (void)aNSString;  
      
    CFRelease(aCFString);  
} 
```