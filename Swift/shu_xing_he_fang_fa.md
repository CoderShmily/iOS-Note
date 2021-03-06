# 属性
```
Swift中如果文件在同一个命名空间下是不需要导入头文件的
同一个命名空间可以理解为同一个工程
默认情况下, 项目名称就是命名空间的名称,如果想要修改命名空间可以在详见截图
```
![项目名-命名空间](项目名-- 命名空间.png) 


```swift
var _name: String?
var name: String?{
    get{
        print("get")
        return _name
    }
    set{
        print("set")
        // newValue是系统提供给我们的, 只要重写set方法, 就可以通过newValue拿到外界设置的值
        _name = newValue
    }
}


var name: String?
    {
    didSet{
        print(self)
        // 设置完毕之后就会调用
        name = "cool"
    }
}

// 计算属性, 计算属性不具备存储功能
var age: Int?{
    get{
        return 100
    }
}
```

# getter 和 setter

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

```

```objc
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
# 因为默认的init没了,所以创建对象要用默认的init(),要override init
    
}
```

```swift
// 传入字典构造对象
init(dict: [NSObject: AnyObject]) {
    name = dict["name"] as? String
    age = dict["age"] as? Int
}

init(dict:[String: AnyObject])
{
    // 注意: 在构造方法中使用KVC必须先调用super.init()之后才能使用
    // 只有调用了super.init系统才会为属性分配存储空间
    super.init()
    
    // 特点: 字典的key必须和模型的属性一一对应
    setValuesForKeysWithDictionary(dict)
}
    
// 利用KVC赋值时, 只要找不到对应的key就会调用这个属性
override func setValue(value: AnyObject?, forUndefinedKey key: String) {
}
```

## 析构函数

```swift
// 相当于OC中的dealloc方法, 对象释放的时候就会调用该方法,
// 这个方法中主要进行一些资源的释放操作
deinit {
    print("88")
}
```
## 重写description
```swift
// 重写打印对象属性
// 如果是计算型的属性, 可以简写
let properties = ["name", "age"]
override var description: String{
//        return "name = \(name), age = \(age)"
   let dict = dictionaryWithValuesForKeys(properties)
   return "\(dict)"
}
```

