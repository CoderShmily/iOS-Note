# plist存储

`什么时候使用plist存储：字典或者数组`
`plist不能存储自定义对象`

```objc
- (IBAction)save:(id)sender {
    Person *p = [[Person alloc] init];

    // 假设存储数组，存储应用沙盒
    // 文件保存到文件夹
    NSArray *users = @[p,@"332"];

    // directory:获取哪个文件夹 NSCachesDirectory,搜索caches文件
    // domainMask:获取哪个范围下文件夹 NSUserDomainMask,表示在当前用户下去搜索
    // expandTilde:是否展开全路径
    // ~/Library/Caches
    //获取Caches文件夹的路径
    NSString *cachesPath =  NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES)[0];


    // 拼接文件路径
    NSString *filePath = [cachesPath stringByAppendingPathComponent:@"user.plist"];


    NSLog(@"%@",cachesPath);

    // File文件全路 径
    [users writeToFile:filePath atomically:YES];

}
- (IBAction)read:(id)sender {
    // plist读取：之前是对象怎么存的，读取出来也是什么数组
    NSString *cachesPath =  NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES)[0];

    // 拼接文件路径
    NSString *filePath = [cachesPath stringByAppendingPathComponent:@"user.plist"];
    NSArray *arr = [NSArray arrayWithContentsOfFile:filePath];

}

```
