# carlinksone
```objc
苹果开发者账号	martinx@carlinksone.com	carlinksoneiosDev01
- info.plst增加Bundle display name字段 可以另起程序显示名字

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




#出现这种状态是因为键盘的frame导致的UITableView的frame发生变化

解决办法
- (void)searchDisplayController:(UISearchDisplayController *)controller didHideSearchResultsTableView:(UITableView *)tableView {
    [[NSNotificationCenter defaultCenter] removeObserver:self name:UIKeyboardWillHideNotification object:nil]; 
}
 
- (void)searchDisplayController:(UISearchDisplayController *)controller willShowSearchResultsTableView:(UITableView *)tableView {
 
    [[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(keyboardWillHide) name:UIKeyboardWillHideNotification object:nil];
 
}

- (void) keyboardWillHide {
    UITableView *tableView = [[self searchDisplayController] searchResultsTableView];
    [tableView setContentInset:UIEdgeInsetsZero];
    [tableView setScrollIndicatorInsets:UIEdgeInsetsZero];
 
}
```