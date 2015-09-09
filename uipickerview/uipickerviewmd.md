# UIPickerView


<1>注意点：PickerView的高度不能改，默认162，PickerView里面每行的高度可以改，不要弄混淆了。

<2> 如何使用PickerView展示数据？ 让控制器作为PickerView的数据源，控制器遵守PickerView的数据源方法。

➣注意：如果没有返回每一行长什么样子，每行就会显示?，看见?,就知道没有实现每一行长什么样子的方法。

<3> PickerView的数据源方法
```objc
// 返回显示的列数
- (NSInteger)numberOfComponentsInPickerView:(UIPickerView *)pickerView;

// 返回显示的行数
- (NSInteger)pickerView:(UIPickerView *)pickerView numberOfRowsInComponent:(NSInteger)component;
```
<4> PickerView的代理方法

```objc
// 第component列第row行的展示标题
- (NSString *)pickerView:(UIPickerView *)pickerView titleForRow:(NSInteger)row forComponent:(NSInteger)component

// 第component列第row行带属性的标题
- (NSAttributedString *)pickerView:(UIPickerView *)pickerView attributedTitleForRow:(NSInteger)row forComponent:(NSInteger)
component

// 第component列第row行展示的视图
- (UIView *)pickerView:(UIPickerView *)pickerView viewForRow:(NSInteger)row forComponent:(NSInteger)component reusingView:(UIView *)view;

// 返回第component列每一行的高度和宽度
- (CGFloat)pickerView:(UIPickerView *)pickerView widthForComponent:(NSInteger)component;
- (CGFloat)pickerView:(UIPickerView *)pickerView rowHeightForComponent:(NSInteger)component;

// 选中第component列第row行调用
- (void)pickerView:(UIPickerView *)pickerView didSelectRow:(NSInteger)row inComponent:(NSInteger)component;
```
<5> 问题：label没有显示最新选中的一行。

原因：手动调用pickview滚动，选中某一行，不会触发代理，我们自己主动调用代理，让lebel显示选中哪一行.

➣注意：只有用户手动滚动才可以触发pickview的代理方法。


