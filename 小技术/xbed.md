# Xbed

```objc
#define IS_IPHONE_3_5Inche (IS_IPHONE && [UIScreen mainScreen].bounds.size.width == 320 &&[UIScreen mainScreen].bounds.size.height == 480)
#define IS_IPHONE_4_0Inche (IS_IPHONE && [UIScreen mainScreen].bounds.size.width == 320 &&[UIScreen mainScreen].bounds.size.height == 568)
#define IS_IPHONE_4_7Inche (IS_IPHONE && [UIScreen mainScreen].bounds.size.width == 375 &&[UIScreen mainScreen].bounds.size.height == 667)
#define IS_IPHONE_5_5Inche (IS_IPHONE && [UIScreen mainScreen].bounds.size.width == 414 &&[UIScreen mainScreen].bounds.size.height == 736)
```
```objc
// 带有navigationController的从导航栏下面开始布局
self.navigationController.navigationBar.translucent = NO;


self.automaticallyAdjustsScrollViewInsets = NO;

self.edgesForExtendedLayout = UIRectEdgeNone;

// 手动布局要用 masonry默认设置了
self.translatesAutoresizingMaskIntoConstraints = NO;
```
edit断点设置 条件

设置观察点

watchpoint set variable self->_test->_var   注意写法，一定要用->而且，要用下划线取得变量。不能用" . "。
再配合条件断点，在某些情况下，调试起来非常有效：下面demo显示了：“在_var == 2的时候才断点”的用法
watchpoint set variable

```objc
+(void)AFRequestWithURL:(NSString *)URLString method:(NSString *)httpMethod
             parameters:(NSMutableDictionary*)paramDic
                success:(void (^)(AFHTTPRequestOperation *operation, id responseObject))success
                failure:(void (^)(AFHTTPRequestOperation *operation, NSError *error))failure
{
    NSData* jsonData = [NSJSONSerialization dataWithJSONObject:paramDic options:kNilOptions error:nil];
    NSString *jsonStr = [[NSString alloc] initWithData:jsonData encoding:NSUTF8StringEncoding];
    
    NSMutableDictionary *dic = [[NSMutableDictionary alloc] init];
    [dic setValue:jsonStr forKey:@"json_data"];
    
    AFHTTPRequestOperationManager *manager = [AFHTTPRequestOperationManager manager];
    manager.responseSerializer = [AFHTTPResponseSerializer serializer];
    manager.requestSerializer = [AFHTTPRequestSerializer serializer];
    
    if ([httpMethod isEqualToString:GET]) {
        [manager GET:URLString parameters:dic success:^(AFHTTPRequestOperation *operation, id responseObject) {
            NSString *string = [[NSString alloc] initWithData:responseObject encoding:NSUTF8StringEncoding];
            NSLog(@"JSON: %@", string);
            NSDictionary* dic = [NSDictionary dictionaryWithDictionary:[string JSONValue]];
            NSInteger retcode = [[dic objectForKey:@"retcode"] integerValue];
            if (retcode > 0) {
                success(operation,dic);
            }else{
                NSError* error = [NSError errorWithDomain:[dic objectForKey:@"retmsg"] code:retcode userInfo:nil];
                failure(operation,error);
            }
        } failure:failure];
    }else if ([httpMethod isEqualToString:POST]){
        [manager POST:URLString parameters:dic success:^(AFHTTPRequestOperation *operation, id responseObject) {
            NSString *string = [[NSString alloc] initWithData:responseObject encoding:NSUTF8StringEncoding];
            NSLog(@"JSON: %@", string);
            NSDictionary* dic = [NSDictionary dictionaryWithDictionary:[string JSONValue]];
            NSInteger retcode = [[dic objectForKey:@"retcode"] integerValue];
            if (retcode > 0) {
                success(operation,dic);
            }else{
                NSError* error = [NSError errorWithDomain:[dic objectForKey:@"retmsg"] code:retcode userInfo:nil];
                failure(operation,error);
            }
        } failure:failure];
    }
}


sd_setImageWithURL
```
