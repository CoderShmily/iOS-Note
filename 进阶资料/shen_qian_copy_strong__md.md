![](深浅copy.png)

- copy 出来副本都是不可变
- mutableCopy 出来副本都是可变
- 不可变+copy 才会出来浅拷贝 

![](Snip20150930_1.png)
#### `注意:以数组对象为例,左边是源对象,中间是执行的操作,右边是生成的副本`
---

![](Snip20150930_3.png)

<font color = red>综上所述,深浅拷贝只是针对集合类对象本身而言,集合类里面包含的对象并没有进行深拷贝,都还是指向相同的地址</font>

---

### 执行真正的深拷贝
```objc
 // 下面两个写法:生成的类型跟array无关,alloc已经决定类型和地址,向对象都发送copy消息
 NSArray *array1= [[NSArray alloc] initWithArray:array copyItems:YES];
 NSMutableArray *array1 = [[NSMutableArray alloc] initWithArray:array copyItems:YES];
```