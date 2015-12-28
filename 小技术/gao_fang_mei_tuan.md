# 高仿美团
```objc
AFJSONResponseSerializer(AFXMLDocumentResponseSerializer/AFXMLParserResponseSerializer/AFPropertyListResponseSerializer)
self.acceptableContentTypes = [NSSet setWithObjects:@"application/json", @"text/json", @"text/javascript", nil];
//完整的
manager.responseSerializer.acceptableContentTypes = [NSSet setWithObjects:@"application/json",@"text/json", @"text/plain", @"text/html", nil];

manager.responseSerializer = [AFJSONResponseSerializer serializer];//申明返回的结果是json类型
manager.responseSerializer.acceptableContentTypes = [NSSet setWithObject:@"text/html"];//如果报接受类型不一致请替换一致text/html或别的
manager.requestSerializer=[AFJSONRequestSerializer serializer];//申明请求的数据是json类型

content-type: text/plain 最好添加manager.responseSerializer = [AFHTTPResponseSerializer serializer];//使用这个将得到的是NSData
尽量不改acceptableContentTypes

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