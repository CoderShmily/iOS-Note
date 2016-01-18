# carlinksone
```objc
邮箱zh@carlinksone.com 15841584
https://carlinksone/svn/clwx/clwx/product     
zhanghao   zhanghao


bundle ID:com.carlinksone.carapp

百度Appkey
测试：kCePZEM4uwsWvNckGaS2211Z
生产：EtgNdsdhheHoZsjGY4pvLlas

云通讯http://www.yuntongxun.com
shixiang@carlinksone.com   chexiang2015
APP ID:8a48b5515018a0f401505aa20b1779e7
APP TOKEN:6445ae12051e3bf7bfe17771f9a16861


苹果开发者账号	martinx@carlinksone.com	carlinksoneiosDev01

生产
SERVER_URL = "http://www.carlinksone.com/imweb";
UPLOAD_PIC_URL = "http://www.carlinksone.com/imweb";

测试
SERVER_URL = "http://192.168.2.20:8080/imweb";
UPLOAD_PIC_URL = "http://192.168.2.20:8080/imweb";


1-16 是以前的接口   17以后是v1.1新增的 结合看

---------------------------------------------
APP ID:8a48b5515018a0f401505aa20b1779e7
APP TOKEN:6445ae12051e3bf7bfe17771f9a16861


id @(20094)
//    @"app_id" : @"2015000002"
//    uid int 用户id
//    app_id string 应用id
//    sign string 签名
//    ver string 客户端版本号

签名
    NSDictionary *dict2 = @{
                            @"app_id" : APP_ID,
                            @"id" : @(20094)
                           };
    [MHNetworkManager postReqeustWithURL:REQUEST_URL(@"/api/user/get") params:[dict2 getSignDict] successBlock:^(NSDictionary *returnData) {
        
        NSLog(@"_________1_______________%@", returnData);
    } failureBlock:^(NSError *error) {
        
    }];
    
    
        // id排序
    [MHNetworkManager postReqeustWithURL:REQUEST_URL(@"/api/common/location/all/list") params:nil successBlock:^(NSDictionary *returnData) {
        
        NSArray *result = [returnData[@"data"] sortedArrayUsingSelector:@selector(compareMethodWithDict:)];
        
        for (NSDictionary *dict in result) {
            NSLog(@"%@", dict[@"id"]);
        }
    } failureBlock:^(NSError *error) {
        
    }];
    ```

- info.plst增加Bundle display name字段 可以另起程序显示名字

```objc
1.单张图上传

AFHTTPRequestOperationManager *manager = [AFHTTPRequestOperationManager manager];    [manager POST:urlString parameters:params constructingBodyWithBlock:^(id_Nonnull formData) {

//使用日期生成图片名称

NSDateFormatter *formatter = [[NSDateFormatter alloc] init];
formatter.dateFormat = @"yyyy-MM-dd HH:mm:ss";
NSString *fileName = [NSString stringWithFormat:@"%@.png",[formatter stringFromDate:[NSDate date]]];
[formData appendPartWithFileData:imageData name:@"uploadFile" fileName:fileName mimeType:@"image/png"];
} success:^(AFHTTPRequestOperation * _Nonnull operation, id  _Nonnull responseObject) {

//上传图片成功执行回调

completion(responseObject,nil);

} failure:^(AFHTTPRequestOperation * _Nonnull operation, NSError * _Nonnull error) {

//上传图片失败执行回调

completion(nil,error);

}];

2.多图上传

多图上传和单图上传区别在于文件名称

AFHTTPRequestOperationManager *manager = [AFHTTPRequestOperationManager manager];    [manager POST:urlString parameters:params constructingBodyWithBlock:^(id_Nonnull formData) {

NSInteger imgCount = 0;

for (NSData *imageData in imageDatas) {

NSDateFormatter *formatter = [[NSDateFormatter alloc] init];

formatter.dateFormat = @"yyyy-MM-dd HH:mm:ss:SSS";

NSString *fileName = [NSString stringWithFormat:@"%@%@.png",[formatter stringFromDate:[NSDate date]],@(imgCount)];

[formData appendPartWithFileData:imageData name:[NSString stringWithFormat:@"uploadFile%@",@(imgCount)] fileName:fileName mimeType:@"image/png"];

imgCount++;

}

} success:^(AFHTTPRequestOperation * _Nonnull operation, id  _Nonnull responseObject) {

completion(responseObject,nil);

} failure:^(AFHTTPRequestOperation * _Nonnull operation, NSError * _Nonnull error) {

completion(nil,error);

}];
```