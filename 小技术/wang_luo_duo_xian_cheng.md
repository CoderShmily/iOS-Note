# 网络多线程
###NSURLConnection的代理方法执行线程
```OBJC
NSURLRequest *request = [NSURLRequest requestWithURL:[NSURL URLWithString:@"https://www.baidu.com/"]];

// 该方法内部其实会将connection对象作为一个source添加到当前的runloop中，指定运行模式为默认，所以子线程创建对象时，要先手动开启runloop,而且模式必须为默认模式.
NSURLConnection *connection = [NSURLConnection connectionWithRequest:request delegate:self];

// NSURLConnection *connection = [[NSURLConnection alloc] initWithRequest:request delegate:self];

// 最后参数设置为NO时，要调用对象start方法才会发请求，如果调用了start方法，这个请求可以在子线程运行，因为start方法在内部开启了runloop。
// NSURLConnection *connection = [[NSURLConnection alloc] initWithRequest:request delegate:self startImmediately:YES];

//[connection setDelegateQueue:[[NSOperationQueue alloc] init]]; // 默认代理方法在主线程执行， 此处可以设置在子线程执行，此处不能设置为[NSOperationQueue mainQueue];
```
###NSURLSession
- NSURLSessionTask
  - NSURLSessionDataTask
    - NSURLSessionUploadTask
  - NSURLSessionDownloadTask

####NSURLSession get请求
```objc
//1.确定URL
NSURL *url = [NSURL URLWithString:@"http://"];

//2.创建请求对象
NSURLRequest *request =[NSURLRequest requestWithURL:url];

//3.创建会话对象
NSURLSession *session = [NSURLSession sharedSession];

//4.创建Task
/*
 第一个参数:请求对象
 第二个参数:completionHandler 当请求完成之后调用
    data:响应体信息
    response:响应头信息
    error:错误信息当请求失败的时候 error有值
 */
NSURLSessionDataTask *dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {

    //6.解析数据
    NSLog(@"%@",[[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding]);
}];

//5.执行Task
[dataTask resume];
```
--- 
```objc
//1.确定URL
NSURL *url = [NSURL URLWithString:@"http://"];

//2.创建请求对象
//NSURLRequest *request =[NSURLRequest requestWithURL:url];

//3.创建会话对象
NSURLSession *session = [NSURLSession sharedSession];

//4.创建Task
/*
 第一个参数:请求路径
 第二个参数:completionHandler 当请求完成之后调用
 data:响应体信息
 response:响应头信息
 error:错误信息当请求失败的时候 error有值
 注意:dataTaskWithURL 内部会自动的将请求路径作为参数创建一个请求对象(GET)
 */
NSURLSessionDataTask *dataTask = [session dataTaskWithURL:url completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {

    //6.解析数据
    NSLog(@"%@",[[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding]);
}];

//5.执行Task
[dataTask resume];
```
####NSURLSession post请求
```objc
//1.确定URL
NSURL *url = [NSURL URLWithString:@"http://"];

//2.创建请求对象
NSMutableURLRequest *request =[NSMutableURLRequest requestWithURL:url];

//2.1 设置请求方法为post
request.HTTPMethod = @"POST";

//2.2 设置请求体
request.HTTPBody = [@"username=xxx" dataUsingEncoding:NSUTF8StringEncoding];

//3.创建会话对象
NSURLSession *session = [NSURLSession sharedSession];

//4.创建Task
/*
 第一个参数:请求对象
 第二个参数:completionHandler 当请求完成之后调用 !!! 在子线程中调用
 data:响应体信息
 response:响应头信息
 error:错误信息当请求失败的时候 error有值
 */
NSURLSessionDataTask *dataTask = [session dataTaskWithRequest:request completionHandler:^(NSData * _Nullable data, NSURLResponse * _Nullable response, NSError * _Nullable error) {
    
    NSLog(@"%@",[NSThread currentThread]);
    //6.解析数据
    NSLog(@"%@",[[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding]);
}];

//5.执行Task
[dataTask resume];
```

####NSURLSession代理方法
```objc
-(void)touchesBegan:(NSSet<UITouch *> *)touches withEvent:(UIEvent *)event
{
    //1.url
    NSURL *url = [NSURL URLWithString:@"http://120.25.226.186:32812/login?username=123&pwd=123&type=JSON"];
    
    //2.创建请求对象
    NSURLRequest *request = [NSURLRequest requestWithURL:url];
    
    //3.创建会话对象,设置代理
    /*
     第一个参数:配置信息 [NSURLSessionConfiguration defaultSessionConfiguration]
     第二个参数:代理
     第三个参数:设置代理方法在哪个线程中调用
     */
    NSURLSession *session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration defaultSessionConfiguration] delegate:self delegateQueue:[NSOperationQueue mainQueue]];
    
    //4.创建Task
    NSURLSessionDataTask *dataTask = [session dataTaskWithRequest:request];
    
    //5.执行Task
    [dataTask resume];
}

#pragma mark ----------------------
#pragma mark NSURLSessionDataDelegate
/**
 *  1.接收到服务器的响应 它默认会取消该请求
 *
 *  @param session           会话对象
 *  @param dataTask          请求任务
 *  @param response          响应头信息
 *  @param completionHandler 回调 传给系统
 */
-(void)URLSession:(NSURLSession *)session dataTask:(NSURLSessionDataTask *)dataTask didReceiveResponse:(NSURLResponse *)response completionHandler:(void (^)(NSURLSessionResponseDisposition))completionHandler
{
    NSLog(@"%s",__func__);
    
    /*
     NSURLSessionResponseCancel = 0,取消 默认
     NSURLSessionResponseAllow = 1, 接收
     NSURLSessionResponseBecomeDownload = 2, 变成下载任务
     NSURLSessionResponseBecomeStream        变成流
     */
    completionHandler(NSURLSessionResponseAllow);
}

/**
 *  接收到服务器返回的数据 调用多次
 *
 *  @param session           会话对象
 *  @param dataTask          请求任务
 *  @param data              本次下载的数据
 */
-(void)URLSession:(NSURLSession *)session dataTask:(NSURLSessionDataTask *)dataTask didReceiveData:(NSData *)data
{
     NSLog(@"%s",__func__);
    
    //拼接数据
    [self.fileData appendData:data];
}

/**
 *  请求结束或者是失败的时候调用
 *
 *  @param session           会话对象
 *  @param dataTask          请求任务
 *  @param error             错误信息
 */
-(void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task didCompleteWithError:(NSError *)error
{
     NSLog(@"%s",__func__);
    
    //解析数据
    NSLog(@"%@",[[NSString alloc]initWithData:self.fileData encoding:NSUTF8StringEncoding]);
}
```

####NSURLSession 下载文件（1）
```objc
//优点:不需要担心内存
//缺点:无法监听文件下载进度
-(void)download
{
//1.url
NSURL *url = [NSURL URLWithString:@"http://120.25.226.186:32812/resources/images/minion_03.png"];

//2.创建请求对象
NSURLRequest *request = [NSURLRequest requestWithURL:url];

//3.创建session
NSURLSession *session = [NSURLSession sharedSession];

//4.创建Task
/*
 第一个参数:请求对象
 第二个参数:completionHandler 回调
    location:
    response:响应头信息
    error:错误信息
 */
//该方法内部已经实现了边接受数据边写沙盒(tmp)的操作
NSURLSessionDownloadTask *downloadTask = [session downloadTaskWithRequest:request completionHandler:^(NSURL * _Nullable location, NSURLResponse * _Nullable response, NSError * _Nullable error) {
    
    //6.处理数据
    NSLog(@"%@---%@",location,[NSThread currentThread]);
    
    //6.1 拼接文件全路径
    NSString *fullPath = [[NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES) lastObject] stringByAppendingPathComponent:response.suggestedFilename];
    
    //6.2 剪切文件
    [[NSFileManager defaultManager]moveItemAtURL:location toURL:[NSURL fileURLWithPath:fullPath] error:nil];
    NSLog(@"%@",fullPath);
}];

//5.执行Task
[downloadTask resume];
}
```