# AFNetworking
```objc
 1. AFHTTPRequestOperationManager *manager = [AFHTTPRequestOperationManager manager]; 
  
    所有的网络请求,均有manager发起 
  
 2. 需要注意的是,默认提交请求的数据是二进制的,返回格式是JSON 
  
    1> 如果提交数据是JSON的,需要将请求格式设置为AFJSONRequestSerializer 
    2> 如果返回格式不是JSON的设置responseSerializer为对应类型
  
 3. 请求格式 
  
     AFHTTPRequestSerializer            二进制格式 
     AFJSONRequestSerializer            JSON 
     AFPropertyListRequestSerializer    PList(是一种特殊的XML,解析起来相对容易) 
  
 4. 返回格式 
  
     AFHTTPResponseSerializer           二进制格式 
     AFJSONResponseSerializer           JSON 
     AFXMLParserResponseSerializer      XML,只能返回XMLParser,还需要自己通过代理方法解析 
     AFXMLDocumentResponseSerializer (Mac OS X) 
     AFPropertyListResponseSerializer   PList 
     AFImageResponseSerializer          Image 
     AFCompoundResponseSerializer       组合 
     
     1. 默认返回JSON
     manager.responseSerializer = [AFJSONResponseSerializer serializer];
     2. 返回XML
     manager.responseSerializer = [AFXMLParserResponseSerializer serializer];
     3. 返回数据不是JSON,XML,比如是html,text等 
     manager.responseSerializer = [AFHTTPResponseSerializer serializer];
     使用，此时返回的数据是NSData
     比如是html还要在下面代码添加@"text/html"
     manager.responseSerializer.acceptableContentTypes = [NSSet setWithObjects:@"application/json",@"text/json", @"text/plain", @"text/html", nil];
//如果报接受类型text/plain错误尝试添加，application/octet-stream


//header 设置
    [manager.requestSerializer setValue:K_PASS_IP forHTTPHeaderField:@"Host"];
    [manager.requestSerializer setValue:@"max-age=0" forHTTPHeaderField:@"Cache-Control"];
    [manager.requestSerializer setValue:@"text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8" forHTTPHeaderField:@"Accept"];
    [manager.requestSerializer setValue:@"zh-cn,zh;q=0.8,en-us;q=0.5,en;q=0.3" forHTTPHeaderField:@"Accept-Language"];
    [manager.requestSerializer setValue:@"gzip, deflate" forHTTPHeaderField:@"Accept-Encoding"];
    [manager.requestSerializer setValue:@"keep-alive" forHTTPHeaderField:@"Connection"];
    [manager.requestSerializer setValue:@"Mozilla/5.0 (Macintosh; Intel Mac OS X 10.10; rv:35.0) Gecko/20100101 Firefox/35.0" forHTTPHeaderField:@"User-Agent"];

 
// PNG --> data
[UIImagePNGRepresentation(image) writeToFile:filePath atomically:YES];
```

