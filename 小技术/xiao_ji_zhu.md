```objc
xcode5（iOS7）? image.xcassets 里面有个lunch文件夹放图片

图片拉伸代码有三种 xcode有三种?
```

## 多线程
iPhone OS下的主线程的堆栈大小是1M，第二个线程开始就是512KB，并且该值不能通过编译器开关或线程API函数来更改，只有主线程有直接修改UI的能力

即：主线程最大占1M的栈区空间,每条子线程最大占512K的栈区空间

iOS多线程的应用场景
- 单例模式
- 可以将耗时的执行路径(如网络请求)放在其他线程中执行;
- 



 ##URLSession  
 当程序在前台时,NSURLSession和NSURLConnection大部分可以互相替代.`NSURLSession支持后台网络操作,除非用户强行关闭`.
 session支持后台有block等，自己查
NSURLConnection相比，NSURLsession最直接的改进就是可以配置每个session的缓存，协议，cookie，以及证书策略（credential policy），甚至跨程序共享这些信息。这将允许程序和网络基础框架之间相互独立，不会发生干扰。每个NSURLSession对象都由一个NSURLSessionConfiguration对象来进行初始化，后者指定了刚才提到的那些策略以及一些用来增强移动设备上性能的新选项。
