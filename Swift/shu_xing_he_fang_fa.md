```
Swift中如果文件在同一个命名空间下是不需要导入头文件的
同一个命名空间可以理解为同一个工程
默认情况下, 项目名称就是命名空间的名称, 如果想要修改命名空间可以在详见截图
```

```swift
var _name: String?
var name: String?{
    get{
        print("get")
        return _name
    }
    set{
        print("set")
        // newValue是系统提供给我们的, 只要重写set方法, 就可以通过newValue拿到外界设置的值
        _name = newValue
    }
}


var name: String?
    {
    didSet{
        print(self)
        // 设置完毕之后就会调用
        name = "cool"
    }
}

// 计算属性, 计算属性不具备存储功能
var age: Int?{
    get{
        return 100
    }
}
```