```

class NetworkTools: NSObject {
    // 这样写也是懒加载的
    // 注意: 一定要写let, let本身就是线程安全的, 并且只能赋值一次
    static let instance: NetworkTools = NetworkTools()
    class func shareNetworkTools() -> NetworkTools
    {
        return instance
    }
    
    override init() {
        print("我被调用了")
    }
    
}

```



```
class NetworkTools: NSObject {
    // 这样写也是懒加载的
    // 注意: 一定要写let, let本身就是线程安全的, 并且只能赋值一次
    static let instance: NetworkTools = NetworkTools()
    class func shareNetworkTools() -> NetworkTools
    {
        return instance
    }
    
    override init() {
        print("我被调用了")
    }
    
}
```
