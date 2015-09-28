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

```objc
# 简写:
func loadData(finished: ()->())
{
    // 1.执行耗时操作
    print("耗时操作")
    
    // 2.回调通知调用者执行完毕
    finished()
}
简写:
1.如果闭包没有参数, 并且没有返回值, 那么in和in之前的代码可以省略
2.如故偶闭包是形参列表的最后一个参数, 那么可以将闭包写到圆括号后面
3.如果形参列表只有一个参数, 并且这个参数是闭包, 那么圆括号可以省略
*/
loadData {
    print("收到执行完毕的通知")
}
```

```swift
loadData {
    print("收到执行完毕的通知")
    // 如果在闭包中使用到了外界对象, 必须加上self
//            self.view.backgroundColor = UIColor.redColor()
    weakSelf!.view.backgroundColor = UIColor.redColor()
}

loadData { [weak self] () -> () in
    print("收到执行完毕的通知")
    self!.view.backgroundColor = UIColor.redColor()
}

// Swift中推荐能不写self就不写self,  

// OC中weak特点: 如果对象被释放之后会自动赋值给nil
// OC中的__unsafe_unretained特点: 如果对象被释放之后不会自动赋值为nil
// unowned 相当于OC中的__unsafe_unretained, 并且特点一致
loadData { [unowned self] () -> () in
    print("收到执行完毕的通知")
    self.view.backgroundColor = UIColor.redColor()
}
}

func loadData(finished: ()->())
{
// 0.保存闭包
over = finished

// 1.执行耗时操作
print("耗时操作")

// 2.回调通知调用者执行完毕
finished()
}

// 相当于OC中的dealloc方法, 对象释放的时候就会调用该方法
// 在这个方法中主要进行一些资源的释放操作
deinit{
print("滚")
}
```
