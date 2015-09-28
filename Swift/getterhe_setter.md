# getter & setter

## 自定义 Person 类

```swift
class Person: NSObject {

    var name: String?
    var age: Int?
}
```

## getter & setter

```swift
var _name: String?

var name: String? {
    get {
        return _name
    }
    set {
        _name = newValue
    }
}
```

* 在 `Swift` 中以上形式的 getter & setter 很少用

## didSet

* 在 OC 中，我们通常希望在给某一个变量赋值之后，去做一些额外的操作
* 最经典的应用就是在自定义 Cell 的时候，通过模型的设置方法完成 Cell 的填充

```swift
var length: Int? {
    didSet {
        timeStr = String(format: "%02d:%02d:%02d", arguments: [length! / 3600, (length! % 3600) / 60, length! % 60])
    }
}
var timeStr: String?
```

## 计算型属性

```swift
var title: String {
    get {
        return "Mr " + (name ?? "")
    }
}
```

* 只实现 `getter` 方法的属性被称为计算型属性，等同于 OC 中的 `ReadOnly` 属性
* 计算型属性本身不占用内存空间
* 不可以给计算型属性设置数值
* 计算型属性可以使用以下代码简写

```swift
var title: String {
    return "Mr " + (name ?? "")
}
```

## 构造函数
```
注意点: 如果没有在构造方法中对属性进行初始化,
那么对象类型可以写?,#基本数据类型最好直接赋值一个初始值#

如果在构造方法中给属性赋了初始值, 那么就可以不用写?了,因为只要创建对象就一定会调用构造
方法, 只要调用构造方法那么属性就一定有值

在Swift中, 如果重写父类的方法, 需要加上override修饰
```

```
    // 在Swift中, 如果重写父类的方法, 需要加上override修饰
    override init() {
        name = "lnj"
        age = 3
    }


    // 自定义构造方法
    // 只要自定义了构造方法, 那么系统自带的默认构造方法就会失效
    init(name: String, age: Int)
    {
//        注意: Swift语法规定, 一定要初始化完当前类之后才能初始化父类
        self.name = name
        self.age = age
    // 注意: 系统悄悄的帮我们调用了一次super.init()
    // 因为默认的init没了,所以要用默认的init(),要override init
       
    }
```

```swift
init(dict: [NSObject: AnyObject]) {
    name = dict["name"] as? String
    age = dict["age"] as? Int
}
```

## 析构函数

```swift
deinit {
    print("88")
}
```

