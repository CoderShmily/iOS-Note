# 网络多线程
###NSURLConnection的代理方法执行线程
```OBJC
    NSURLRequest *request = [NSURLRequest requestWithURL:[NSURL URLWithString:@"https://www.baidu.com/"]];
//    NSURLConnection *connection = [NSURLConnection connectionWithRequest:request delegate:self];
    NSURLConnection *connection = [[NSURLConnection alloc] initWithRequest:request delegate:self];
    
// 最后参数设置为NO时，要调用对象start方法才会发请求
//    NSURLConnection *connection = [[NSURLConnection alloc] initWithRequest:request delegate:self startImmediately:YES];
//[connection setDelegateQueue:[[NSOperationQueue alloc] init]]; // 默认代理方法在主线程执行， 此处可以设置在子线程执行，此处不能设置为[NSOperationQueue mainQueue];
```
###
