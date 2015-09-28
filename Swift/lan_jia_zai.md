# 懒加载


* 格式：

```
lazy var 变量: 类型 = { 创建变量代码 }()
```

* 懒加载的写法本质上是定义并执行一个闭包


```
    // 含义: 当dataList被使用到时, 就会执行等号后面的闭包
    // 所以等号后面的闭包的()是必须写的, 如果不写就会报错
    // 注意点: 如果写懒加载, 那么修饰符必须用var
    lazy var dataList:[String] = {
        print("我被加载了")
        return ["lnj", "lmj", "zs"]
    }()
    
```
```
    lazy var satatuses: [String] = self.loadStatus()
    func loadStatus() -> [String]
    {
        print("我被加载了")
        return ["lnj", "lmj", "zs"]
    }
```




