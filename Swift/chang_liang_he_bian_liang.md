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