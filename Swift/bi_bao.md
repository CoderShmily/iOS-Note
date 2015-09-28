# 闭包

* 闭包定义
* 闭包简化 - 尾随闭包
* 闭包参数
* 闭包返回值
* 闭包的循环引用

```
        闭包和OC中的Block差不多
        1.都是用来保存一段代码, 在适当的时候执行
        2.一般用于做一些耗时操作
        3.传递值
        
        类型的格式:  (形参列表)->返回值类型
        in的作用就是用于分隔需要执行的代码
        值的格式: 
        {
            (形参列表)->返回值类型
            in
            需要执行的代码
        }
        闭包和Block的区别:
        Block类似于匿名函数
        闭包就是用来定义函数的, Swift中函数其实就是闭包
    
```

```
//    注意: 以下写法是错误的, 代表闭包的返回值是可选的
/*    var finished: ()->()? */
正解:    var finished: (()->())?
```

```swift
weak var weakSelf = self
demo("zhangsan") { (_) -> Int in
    print(weakSelf?.view.backgroundColor)

    return 20
}
```
