## KVC

- 全称：Key Value Coding（键值编码）
- 赋值

// 能修改、获得私有成员变量
- NSObject(NSKeyValueCoding)

```objc
- (id)valueForKey:(NSString *)key;
- (void)setValue:(id)value forKey:(NSString *)key;

- (NSDictionary *)dictionaryWithValuesForKeys:(NSArray *)keys;
```
 利用KVC字典转模型实现原理：遍历字典中的所有key，去模型中查找有没有跟key相似的属性名。

 1. 遍历出name这个key,去模型中查找有没有setName:方法，找到就直接调用，给模型的成员属性赋值。self.name = dict[@"name"]

 2. 在去模型中查找有没有name这个属性名，如果找到，直接通过属性赋值 name = dict[@"name"]

 3.  在去模型中查找有没有_name这个属性名，如果找到，直接通过属性赋值 _name = dict[@"name"]

 4. 调用setValue:forUndefinedKey:报错。

- (void)setValuesForKeysWithDictionary:(NSDictionary *)keyedValues;

- NSArray(NSKeyValueCoding)

```objc
/* Return an array containing the results of invoking -valueForKey: on each of the receiver's elements. The returned array will contain NSNull elements for each instance of -valueForKey: returning nil.
*/
- (id)valueForKey:(NSString *)key;

/* Invoke -setValue:forKey: on each of the receiver's elements.
*/
- (void)setValue:(id)value forKey:(NSString *)key;

```

- NSDictionary(NSKeyValueCoding)

```objc
/* Return the result of sending -objectForKey: to the receiver.
*/
- (id)valueForKey:(NSString *)key;
```

-  NSMutableDictionary(NSKeyValueCoding)

```objc
/* Send -setObject:forKey: to the receiver, unless the value is nil, in which case send -removeObject:forKey:.
*/
- (void)setValue:(id)value forKey:(NSString *)key;

```
## KVO
- 全称：Key Value Observing（键值监听）
- 作用：监听模型的属性值改变
- 步骤
    - 添加监听器
    ```objc
    // 利用b对象来监听a对象name属性的改变
    [a addObserver:b forKeyPath:@"name" options:NSKeyValueObservingOptionOld | NSKeyValueObservingOptionNew context:@"test"];
    ```
    - 在监听器中实现监听方法
    ```objc
    -(void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary *)change     context:(void *)context
    {
        NSLog(@"%@ %@ %@ %@", object, keyPath, change, context);
    }
    ```
