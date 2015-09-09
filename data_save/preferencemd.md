# 偏好设置存储


-  偏好设置存储`NSUserDefaults`用来专门做偏好设置存储

-  偏好设置存储优点：
    1. 不需要关心文件名，系统会自动帮你生成一个文件名
    2. 快速做键值对的存储

-  读取代码
```objc
- (IBAction)save:(id)sender {

     [[NSUserDefaults standardUserDefaults] setObject:@"123" forKey:@"account"];
     [[NSUserDefaults standardUserDefaults] setBool:YES forKey:@"autoLogin"];
}
- (IBAction)read:(id)sender {

   NSString *str = [[NSUserDefaults standardUserDefaults] objectForKey:@"account"];
   BOOL autoLogin = [[NSUserDefaults standardUserDefaults] boolForKey:@"autoLogin"];
    NSLog(@"%d",autoLogin);
}
```
