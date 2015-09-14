网络笔记

```objc
// 3.1设置请求头, 告诉系统从什么地方开始下载
// 3.1.1获取已经下载数据的大小
NSFileManager *manager = [NSFileManager defaultManager];
NSDictionary *dict = [manager attributesOfItemAtPath:[@"minion_02.mp4" cacheDir] error:nil];
NSInteger fileSize = [dict[NSFileSize] integerValue];
// 3.1.2根据已经下载的大小生成请求头参数值
// 只要设置HTTP请求头的Range属性, 就可以实现从指定位置开始下载
NSString *range = [NSString stringWithFormat:@"bytes=%zd-", fileSize];
[request setValue:range forHTTPHeaderField:@"Range”];
// 2.创建REQUEST
// 注意: 如果需要设置请求体或者其他请求参数, 必须使用NSURLRequest的子类
NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
// 注意点: 必须大写
request.HTTPMethod = @"POST";
// 注意: 只要是POST请求, 系统内部会自动添加?
request.HTTPBody = [@"username=520it&pwd=520it&type=JSON" dataUsingEncoding:NSUTF8StringEncoding];
// 设置超时时间
request.timeoutInterval = 10;
// 设置请求头
[request setValue:@"iOS 9.0" forHTTPHeaderField:@"User-Agent”];

// 3.设置请求头
// 设置请求头的目的: 告诉服务器是上传文件, 而不是传递普通参数
NSString *field = [NSString stringWithFormat:@"multipart/form-data; boundary=%@", kBoundary];
[request setValue:field forHTTPHeaderField:@"Content-Type”];


// 注意: 如果将数据放到request的HTTPBody中, 会被uploadTaskWithRequest方法忽略
// 所以需要上传的数据, 需要放到fromData:中
//    request.HTTPBody = data;

/***********************************************************/


// 设置请求头
[request setValue:[NSString stringWithFormat:@"%zd", data.length] forHTTPHeaderField:@"Content-Length"];
NSURLSessionUploadTask *task = [session uploadTaskWithRequest:request fromData:data completionHandler:^(NSData *data, NSURLResponse *response, NSError *error) {
    NSLog(@"%@", [[NSString alloc] initWithData:data encoding:NSUTF8StringEncoding]);
}];

HTTP协议规定：1个完整的HTTP响应中包含以下内容
请求头：包含了对客户端的环境描述、客户端请求信息等
GET /minion.png HTTP/1.1   // 包含了请求方法、请求资源路径、HTTP协议版本
Host: 120.25.226.186:32812   // 客户端想访问的服务器主机地址
User-Agent: Mozilla/5.0  // 客户端的类型，客户端的软件环境
Accept: text/html, */*   // 客户端所能接收的数据类型
Accept-Language: zh-cn   // 客户端的语言环境
Accept-Encoding: gzip   // 客户端支持的数据压缩格式
请求体：客户端发给服务器的具体数据，比如文件数据(POST请求才会有)
响应头：包含了对服务器的描述、对返回数据的描述
HTTP/1.1 200 OK            // 包含了HTTP协议版本、状态码、状态英文名称
Server: Apache-Coyote/1.1   // 服务器的类型
Content-Type: image/jpeg   // 返回数据的类型
Content-Length: 56811   // 返回数据的长度
Date: Mon, 23 Jun 2014 12:54:52 GMT  // 响应的时间
响应体：服务器返回给客户端的具体数据，比如文件数据

```