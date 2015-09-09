### UITextField相关方法和属性

```objc
[UIView endEditing:YES]; // 可以退出键盘

// 成为第一响应者，可以把光标切换给其他文本框
[UIView becomeFirstResponder];

// 失去第一响应者，文本框若失去，则键盘会退出
[UIView resignFirstResponder];

// 可以设置键盘的辅助控件比如UIButton,UIToolbar
UITextField.inputAccessoryView = UIView;

// 可以自定义弹出的键盘
UITextField.inputView = UIView;
==========================================================
// 是否允许开始编辑，返回NO，不允许编辑，就不会弹出键盘
- (BOOL)textFieldShouldBeginEditing:(UITextField *)textField

// 成为first responder
- (void)textFieldDidBeginEditing:(UITextField *)textField;

// 是否允许结束编辑
- (BOOL)textFieldShouldEndEditing:(UITextField *)textField

//结束编辑,view销毁或者textFieldShouldEndEditing返回YES调用
- (void)textFieldDidEndEditing:(UITextField *)textField

// 是否允许用户输入文字，返回NO，不改变text
- (BOOL)textField:(UITextField *)textField shouldChangeCharactersInRange:(NSRange)range replacementString:(NSString *)string

// called when clear button pressed. return NO to ignore
- (BOOL)textFieldShouldClear:(UITextField *)textField;

// called when 'return' key pressed
- (BOOL)textFieldShouldReturn:(UITextField *)textField;

```

