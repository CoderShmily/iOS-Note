# 函数

## 简单演练

```swift
func sum(a: Int, b: Int) -> Int {
    return a + b
}
```

* 阶段性小结
    * 函数定义格式：`func 函数名(参数: 参数类型...) -> 返回值 { // 代码实现 }`
    * 如果没有返回值， `-> 返回值` 可以省略
    * `->` 是一个很有意思的符号
    * 默认情况下，在调用函数时，第一个参数名是省略的

## 参数名的特殊处理

### 强制要求参数名

```swift
// x,y函数调用的时候显示sum1(x: 10, y: 10)
// a,b函数内部使用
func sum1(x a: Int, y b: Int) -> Int {
    return a + b
}
```

### 省略参数名

```swift
// 不加_调用函数sum1(10, b: 10)
// 加了后调用sum(10,10)
func sum2(a: Int, _ b: Int) -> Int {
    return a + b
}
```
```objc

#没有返回值没有参数的
func say() -> Void
{}
say()

#如果没有返回值还可以简写
// Void == ()
func say2() -> ()
{}

# 如果没有返回值, 那么返回值类型可以省略
func say3()
{}

# 有返回值没有参数的
// 注意点: 返回值类型必须和返回的值保持一致
func getMax() ->Int
{}

# 没有返回值有参数的
// - (void)sum:(int)a b:(int)b;
// [self sum:10 b: 20];
// 从Swift2.0开始, 会自动将第二个参数开始的参数名称作为方法的标签
func sum(a: Int, b: Int)
{
    print("sum = \(a + b)")
}
sum(10, b: 20)

```




