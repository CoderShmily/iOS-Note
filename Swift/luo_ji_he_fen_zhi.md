# 逻辑分支

## 简单体验

```swift
var i = 10

if i > 0 {
    print("OK")
}
```

* 阶段性小结
    * `Swift` 中没有 C 语言中的`非零即真`概念
    * 在逻辑判断时必须显示地指明具体的判断条件
    * `if` 语句条件的 `()` 可以省略
    * 但是 `{}` 不能省略
    
## 三目

```swift
var a = 10
var b = 50

var result = a > b ? a : b
print(result)
```

* 阶段性小结
    * `Swift` 中的 `三目` 运算保持了和 OC 一致的风格

## 可选项

### 演练 1
```swift
let url = NSURL(string: "http://ww.baidu.com/")
