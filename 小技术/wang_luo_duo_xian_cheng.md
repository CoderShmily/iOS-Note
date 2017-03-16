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

####get请求
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
###post请求
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