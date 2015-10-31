```objc
xcode5（iOS7）? image.xcassets 里面有个lunch文件夹放图片

图片拉伸代码有三种 xcode有三种?
```

## 多线程
iPhone OS下的主线程的堆栈大小是1M，第二个线程开始就是512KB，并且该值不能通过编译器开关或线程API函数来更改，只有主线程有直接修改UI的能力

即：主线程最大占1M的栈区空间,每条子线程最大占512K的栈区空间

iOS多线程的应用场景
- `单例模式`
- `可以将耗时的执行路径(如网络请求)放在其他线程中执行`



 ##NSURLSession 和 NSURLConnection
 当程序在前台时,NSURLSession和NSURLConnection大部分可以互相替代.
 
- `NSURLSession支持后台网络操作,除非用户强行关闭`.

- `session支持后台有block等`

- `NSURLsession最直接的改进就是可以配置每个session的缓存，协议，cookie，以及证书策略（credential policy），甚至跨程序共享这些信息。`
这将允许程序和网络基础框架之间相互独立，不会发生干扰。每个NSURLSession对象都由一个NSURLSessionConfiguration对象来进行初始化，后者指定了刚才提到的那些策略以及一些用来增强移动设备上性能的新选项。

- 相比较NSURLConnection的返回处理，NSURLSession提供了灵活的数据返回方式，可以使用简单的block方式来处理返回数据，也可以使用更强大的delegate.

NSURLSession提供的功能:

    1> 通过URL将数据下载到内存;    

    2> 通过URL将数据下载到文件系统;

    3> 将数据上传到指定的URL;

    4> 在后台完成上述功能.

    5> 支持下载,断点续传,后台上传/下载,后台上传/下载任务跟进
    
NSURLSessionTask

    NSURLSession使用NSURLSessionTask来具体执行网络请求的任务.

    NSURLSessionTask支持网络请求的取消、暂停和恢复，比如下载文件暂停之后再恢复就能够自动从上次的进度继续下载 .

    NSURLSessionTask还能获取数据的读取进度 .

    NSURLSessionTask的三种类型:

    1> NSURLSessionDataTask 处理一般的NSData数据对象, 比如通过GET或POST方式从服务器获取JSON或XML返回等等, 但不支持后台获取.

    2> NSURLSessionUploadTask 用于上传文件, 支持后台上传 .

    3> NSURLSessionDownloadTask 用于下载文件, 支持后台下载 .
    
    
    
## iOS设计模式
设计模式的好处：我们可以写出容易理解，重用度很高的代码。降低代码的耦合度，符合软件工程的思想。

设计模式主要分为三类：
- `创造型的`：`单例`和`抽象工厂`。
- `结构类型的`：`MVC`  Decorator, Adapter, Facade and Composite.等这几种模式
- `行为性的`：`Observer`, Memento, Chain of Responsibility and Command.

#### iOS设计模式（常用的）
- `单例设计模式`
- `装饰设计模式的代理设计模式`,装饰设计模式动态的添加行为和责任向一个对象而不修改他的任何代码
- `装饰设计模式的类别设计模式`
- `观察者设计模式`
- 
