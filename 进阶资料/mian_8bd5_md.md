json string --> json data --> json id


- 异常处理

```objc
void uncaughtExceptionHandler(NSException *exception) {
    NSLog(@"CRASH: %@", exception);
    NSLog(@"Stack Trace: %@", [exception callStackSymbols]);
    // Internal error reporting
}

NSSetUncaughtExceptionHandler(&uncaughtExceptionHandler);

```

- 保存硬件设备环境 信息到初始化设置 

```objc
[[NSUserDefaults standardUserDefaults] registerDefaults:dictionary];

```

- 登陆成功或注册后 DBManager存储账户密码到偏好设置

```objc
NSUserDefaults *defaults =[NSUserDefaults standardUserDefaults];
NSString *name = [defaults objectForKey:@"userName"];//根据键值取出name
NSString *phone = [defaults objectForKey:@"userPhone"];//根据键值取出name
NSString *password = [defaults objectForKey:@"password"];//根据键值取出password
```

- 保存用户相关信息到 NSDocumentDirectory的loginModel.txt

```objc
@property (nonatomic ,strong)NSDictionary* lodgerInfo;
@property (nonatomic ,strong)NSString* cardBack;
@property (nonatomic ,strong)NSString* cardFont;
@property (nonatomic ,assign)int createTime;
@property (nonatomic ,strong)NSString* email;
@property (nonatomic ,strong)NSString* idCard;
@property (nonatomic ,assign)int idcardValid;
@property (nonatomic ,strong)NSString* lodgerId;
@property (nonatomic ,strong)NSString* lodgerName;
@property (nonatomic ,strong)NSString* mailingId;
@property (nonatomic ,strong)NSString* mobile;
@property (nonatomic ,strong)NSString* mobileValid;
@property (nonatomic ,strong)NSString* password;
@property (nonatomic ,strong)NSString* password2;
@property (nonatomic ,strong)NSString* salt;
```
---

