![](深浅copy.png)

- copy 出来副本都是不可变
- mutableCopy 出来副本都是可变
- 不可变+copy 才会出来浅拷贝 

#####上图的指针拷贝,内容拷贝名词不是很准确,只为方便理解,具体可参考下面的解释
---

![](Snip20150930_1.png)
#### `注意:以数组对象为例,左边是源对象,中间是执行的操作,右边是生成的副本`
---

![](Snip20150930_3.png)
```
集合类对象是指NSArray、NSDictionary、NSSet ... 之类的对象
非集合类对象指的是 NSString, NSNumber ... 之类的对象
```
<font color = red>
综上所述,深浅拷贝只是针对集合类对象本身而言,集合类里面包含的对象并没有进行深拷贝,都还是指向相同的地址,所以说内容拷贝不太严谨,应该理解为单层深拷贝.对于非集合类,因为只有一层,所以就是深拷贝.
</font>

---

### 执行真正的深拷贝
1.用initWithArray:copyItems: 将第二个参数设置为YES即可深复制
```objc
# 下面两个写法:生成的类型跟array无关,alloc已经决定类型和地址,向对象都发送copy消息
 NSArray *array1= [[NSArray alloc] initWithArray:array copyItems:YES];
 NSMutableArray *array1 = [[NSMutableArray alloc] initWithArray:array copyItems:YES];
```

2.将集合进行归档(archive)，然后解档(unarchive)
```objc
NSArray *trueDeepCopyArray = [NSKeyedUnarchiver unarchiveObjectWithData:[NSKeyedArchiver archivedDataWithRootObject:oldArray]];
```