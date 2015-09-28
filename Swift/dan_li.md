1.笨写法

```
class NetworkTools: NSObject {
// USB写法
static var onceToken: dispatch_once_t = 0
static var instance: NetworkTools?

//    func 相当于OC中的 -
//    class func 相当于OC中的 +
class func shareNetworkTools() -> NetworkTools
{
    print("onceToken = \(onceToken)")
    dispatch_once(&onceToken) { () -> Void in
        print("我被调用了")
        instance = NetworkTools()
    }
    
    return instance!
}
}

```


2.标准写法
```
class NetworkTools: NSObject {
    // 这样写也是懒加载的
    // 注意: 一定要写let, let本身就是线程安全的, 并且只能赋值一次
    static let instance: NetworkTools = NetworkTools()
    class func shareNetworkTools() -> NetworkTools
    {
        return instance
    }
}
```
