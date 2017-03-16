# 网络多线程
###NSURLConnection的代理方法执行线程
```OBJC
NSURLRequest *request = [NSURLRequest requestWithURL:[NSURL URLWithString:@"http://"]];

NSURLConnection *connection = [NSURLConnection connectionWithRequest:request delegate:self];

//[connection setDelegateQueue:[[NSOperationQueue alloc] init]]; // 默认代理方法在主线程执行， 此处可以设置在子线程执行，此处不能设置为[NSOperationQueue mainQueue];
```
###
