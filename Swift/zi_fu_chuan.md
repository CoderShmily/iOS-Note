# 字符串

> 在 Swift 中绝大多数的情况下，推荐使用 String 类型

## 使用 `String` 的原因

* `String` 是一个结构体，性能更高
    * `String` 目前具有了绝大多数 NSString 的功能
    * `String` 支持直接遍历
* `NSString` 是一个 `OC` 对象，性能略差
* `Swift` 提供了 `String` 和 `NSString` 之间的无缝转换

## 遍历字符串

```swift
let str = "我要飞的更High"

for s in str.characters {
    print(s)
}
```

## 字符串拼接

```swift
let str1 = "zhangsan"
let str2 = "lisi"
let i = 10

// 注意: Swift的print函数不支持%i/%@等占位符
// Swift中拼接内容使用 \(需要拼接的数据)
print(str1 + str2)
print("\(str1) \(str2) \(i)")
```

* 阶段性小结
    * 直接在 `""` 中使用 `\(变量名)` 的方式可以快速拼接字符串
    * 小伙伴们再也不要考虑 `stringWithFormat` 了


## 格式化字符串

```swift
for _ in 0...10 {
    let str = String(format: "zhangsan - %04d", arguments: [arc4random_uniform(100)])
    print(str)
}

// 15-09-07
let res2 = String(format: "%02d-%02d-%02d", arguments: [15, 9, 7])
```

* 阶段性小结
    * 在实际开发中，如果需要指定字符串格式，可以使用 `String(format:...)` 的方式
    * 注意：后面的参数需要放在一个数组中

## String & Range 的结合

> 以下是超级费劲的代码

```swift
let str: String = "xiaomage"

// 没写完整,很麻烦
var subStr = str.substringWithRange(Range<String.Index>(start: str.startIndex, end: str.endIndex))
print(subStr)
```

### 建议写法

```swift
// 建议: 在Swift开发中如果要截取字符串,
// 或者以前在OC中使用的方法在Swift字符串中没有,
// 建议先将Swift字符串转换为OC字符串之后再使用
let str1: NSString = "xiaomage"
print(str1.substringWithRange(NSMakeRange(0, 3)))
```

