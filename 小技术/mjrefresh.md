# MJRefresh
```objc

下拉刷新：
view加在x=0 ,w=superW ,y=-h ,h=MJRefreshHeaderHeight(54);

// NSUserDefaults 用时记得 synchronize
[[NSUserDefaults standardUserDefaults] setObject:nil forKey:nil];
[[NSUserDefaults standardUserDefaults] synchronize];
        
```