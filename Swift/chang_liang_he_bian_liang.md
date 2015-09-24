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
let testView = UIView(frame: CGRectMake(0, 0, 100, 100))
testView.backgroundColor = UIColor.redColor()
view.addSubview(testView)
```

* 阶段性小结
    * 在 `Swift` 中要实例化一个对象可以使用 `类名()` 的格式，与 `OC` 中的 `alloc/init` 等价
    * `OC` 中的 `initWithXXX` 在 `Swift` 中通常可以使用 `类名(XXX: )` 找到对应的函数
    * `OC` 中的 `[UIColor redColor]` 类方法，在 `Swift` 中通常可以使用 `类名.XXX` 找到对应的函数
    * 使用 `let` 修饰 `v` 并且赋值，表示 `该常量的内存地址不允许修改，但是可以修改其内部的属性`
    * 当前对象的属性，不需要使用 `self.`