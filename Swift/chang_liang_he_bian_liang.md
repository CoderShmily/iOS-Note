# 常量&变量

```swift
var i = 10
print(i)
i = 15
print(i)

let j = 20

```
- 小结
    * `var` 定义变量，设置之后可以修改
    * `let` 定义常量，设置之后不可以修改
    * 语句末尾不用使用 `;`
    * 在 Swift 中使用 `print()` 替代 OC 中的 `NSLog`
    * `print` 的性能更好，后面会演示

## 定义 `OC` 对象
```swift
// UIView *testView = [[UIView alloc] initWithFrame:];
let testView = UIView(frame: CGRect(x: 0, y: 0, width: 100, height: 100))

testView.backgroundColor = UIColor.redColor()
view.addSubview(testView)
```
```swift
/*
结构体:
OC: CGPointMake()
Swift: CGPoint()
*/
let btn = UIButton(type: UIButtonType.ContactAdd)
btn.center = CGPoint(x: 50, y: 50)
view.addSubview(btn)
```

* 阶段性小结
    * 在 `Swift` 中要实例化一个对象可以使用 `类名()` 的格式，与 `OC` 中的 `alloc/init` 等价
    * `OC` 中的 `initWithXXX` 在 `Swift` 中通常可以使用 `类名(XXX: )` 找到对应的函数
    * `OC` 中的 `[UIColor redColor]` 类方法，在 `Swift` 中通常可以使用 `类名.XXX` 找到对应的函数
    * 使用 `let` 修饰 `v` 并且赋值，表示 `该常量的内存地址不允许修改，但是可以修改其内部的属性`
    * 当前对象的属性，不需要使用 `self.`
    
> 常量&变量的使用原则：尽量先用 let，只有需要变的时候，再用 var，能够更加安全

## 变量类型
```swift
var x = 10
var y = 10.5
var z: Double = 20

print(Double(x) + y)
print(x + Int(y))
print(y + z)
```

* 阶段性小结
    * 初次接触 `Swift` 中会因为简单的 `var` `let` 误以为 `Swift` 中的类型非常松散
    * 其实所有变量的准确类型都是在赋值的同时自动推导的
    * `Swift` 是对类型要求非常严格的一门语言，`一个值永远不会被自动转换成其他类型`
    * 如果要转换，必须显示转换，Swift 中
        * 小数默认是 `Double` 类型
        * 整数默认是 `Int` 类型
    * 如果要显式的指定变量的类型，可以在定义是使用 `var 变量名: 类型 = 值`
