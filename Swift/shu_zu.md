# 数组

## 简单体验

```swift
let arr = ["zhangsan", "lisi"]
print(arr)

// 遍历每一个元素
for a in arr {
    print(a)
}

// 像 OC 一样打印
print(arr as NSArray)

```

## 数组中保存的对象类型

```swift
// 数组中保存的都是字符串
let arr = ["zhangsan", "lisi"]

// 数组中保存的是 NSObject
// 特点: 自动将基本数据类型转换为对象类型
let arr1 = ["zhangsan", 1]
```

* 阶段性小结
    * 数组使用 [] 定义，这一点与 OC 相同
    * 如果初始化时，所有内容类型一致，择数组中保存的是该类型的内容
    * 如果初始化时，所有内容类型不一致，择数组中保存的是 `NSObject`

## 常见数组操作

```objc
// 定义只能保存字符串类型数组
var array: [String]

# 初始化数组
array = ["zhangsan"]

# 添加元素
array.append("lisi")

print(array)

# 删除元素
// 删除, 数组的删除方法会将被删除的元素返回给我们
array.removeAtIndex(1)
print(array)

# 删除所有元素
// keepCapacity: 删除数组中的元素, 是否需要保持数组原有的容量, 如果传入false代表不保持容量
array.removeAll(keepCapacity: true)
print(array.capacity)

// 注意数组容量的变化
// 注意点: 数组添加容量永远是在原有容量的基础上*2
// 放得下继续放,放不下时,在此时的capacity * 2
for i in 0..<10 {
    array.append("\(i)")
    print("\(array) --- \(array.capacity)")
}

# 实例化新的数组
var array2 = [String]()
array2.append("1")
array2.append("2")

# 拼接数组 数组类型要相同
array += array2

```

* 阶段性小结
    * 如果定义数组时指定了保存对象的类型，择不能向数组中添加其他类型的内容
    * 可以使用 `[String]()`
    * `let` 定义的数组是`不可变的`
    * `var` 定义的数组是`可变的`

