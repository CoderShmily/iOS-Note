# 笔记暂存

# 书籍

- OS应用安全攻防实战  
- 黑客攻防技术宝典ios实战篇  
- 深入解析Mac OS X & iOS操作系统


# 网络
```
surge可以抓包 charles只能抓http(https比较麻烦)

git@120.25.106.243:/apps/repo/xbed_ios.git
id_rsa  id_rsa.pub
chmod  600  id_rsa chmod  644  id_rsa.pub





```
#语法
```objc
这个版本更新巨大，不仅带来了 Swift 2.0，OC 的集合类型还增加了对泛型的支持，如数组可以这样申明：
1. @property NSMutableArray<NSDate*> *objects;

UserDefaults是根据时间戳定时的把缓存中的数据写入本地磁盘，而非即时写入。为了防止数据丢失，我们在对重要的数据保存时使用synchornize方法强制写入。但是也要注意，不用频繁的使用synchornize，这样毕竟影响性能和效率
```
#调试
```objc
edit断点设置 条件
设置观察点
watchpoint set variable self->_test->_var   注意写法，一定要用->而且，要用下划线取得变量。不能用" . "。
再配合条件断点，在某些情况下，调试起来非常有效：下面demo显示了：“在_var == 2的时候才断点”的用法
watchpoint set variable
```
#技术
```objc
// 音视频
tlc  rtvideo  ffmpeg

```