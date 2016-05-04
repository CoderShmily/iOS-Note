# error
####error1
```objc
#Warning: Attempt to present <UIImagePickerController: 0x7ca5dc00> on <UIViewController: 0x7b9cac00> which is already presenting (null)

方法1：
在iOS8以前的方法里，直接在
-(void) actionSheet:(UIActionSheet *)actionSheet clickedButtonAtIndex:(NSInteger)buttonIndex;
Click的委托事件里处理就好了，但是在iOS8，系统会抛出警告
Warning: Attempt to present <UIImagePickerController: 0x292b400>on <**Controller: 0x723c150> which is already presenting <UIAlertController: 0xd37b8b0>
并且取消弹出ImagePicker行为。
原因在警告里说得比较明白了，因为已经有actionsheet存在了，不能present新的。此时我们选择新的委托方法
- (void)actionSheet:(UIActionSheet *)actionSheet didDismissWithButtonIndex:(NSInteger)buttonIndex;


方法2：
UIImagePickerCopntroller must be presented in popover in iPad. take a look at the following code for iPad:

    if ([[UIDevice currentDevice] userInterfaceIdiom] == UIUserInterfaceIdiomPad) {
    UIPopoverController *popover = [[UIPopoverController alloc] initWithContentViewController:picker];
    [popover presentPopoverFromRect:self.selectedImageView.bounds inView:self.selectedImageView permittedArrowDirections:UIPopoverArrowDirectionAny animated:YES];
    self.popOver = popover;
} else {
    [self presentModalViewController:picker animated:YES];
}
don't forget to add a strong property for the popover:

@property (nonatomic, strong) UIPopoverController *popOver;
here are the delegate methods for dismissing it:

-(void)imagePickerController:(UIImagePickerController *)picker didFinishPickingMediaWithInfo:(NSDictionary *)info 

-(void)imagePickerControllerDidCancel:(UIImagePickerController *)picker
```

####error2
A页面有输入框输入文字 点击后退出键盘 push进B页面弹窗 点击弹窗按钮会出现键盘，然后消失