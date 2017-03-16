# 网络多线程
###NSURLConnection的代理方法执行线程
```OBJC
NSURLRequest *request = [NSURLRequest requestWithURL:[NSURL URLWithString:@"https://www.baidu.com/"]];

// 该方法内部其实会将connection对象作为一个source添加到当前的runloop中，指定运行模式为默认，所以子线程创建对象时，要先手动开启runloop,而且模式必须为默认模式.
NSURLConnection *connection = [NSURLConnection connectionWithRequest:request delegate:self];

// NSURLConnection *connection = [[NSURLConnection alloc] initWithRequest:request delegate:self];

// 最后参数设置为NO时，要调用对象start方法才会发请求
// NSURLConnection *connection = [[NSURLConnection alloc] initWithRequest:request delegate:self startImmediately:YES];

//[connection setDelegateQueue:[[NSOperationQueue alloc] init]]; // 默认代理方法在主线程执行， 此处可以设置在子线程执行，此处不能设置为[NSOperationQueue mainQueue];
```
###
