### UIDatePicker 和 UITextField代理

<1> UIDatePicker重要属性
```objc
// 获取当前的时间
@property (nonatomic, retain) NSDate *date

// 设置日期模式
@property (nonatomic) UIDatePickerMode datePickerMode;
```
======================================================
### `综合运用：自定义生日键盘`
```objc
#import "ViewController.h"

@interface ViewController ()<UITextFieldDelegate>
@property (weak, nonatomic) IBOutlet UITextField *birthdayField;
@property (weak, nonatomic) IBOutlet UITextField *cityField;
@property (nonatomic, weak) UIDatePicker *datePicker;

@end

@implementation ViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view, typically from a nib.
    // 设置文本框的代理，拦截用户的输入
    _birthdayField.delegate = self;
    _cityField.delegate = self;

    // 自定义生日键盘
    [self setUpBirthdayKeyboard];
}
#pragma mark - 自定义生日键盘
- (void)setUpBirthdayKeyboard
{
    // 创建UIDatePicker
    UIDatePicker *datePicker = [[UIDatePicker alloc] init];

    // 设置datePicker的日期模式
    datePicker.datePickerMode = UIDatePickerModeDate;

    // 设置本地区域 如果日期键盘显示英文，可以设置为中文
    datePicker.locale = [NSLocale localeWithLocaleIdentifier:@"zh"];

// 继承自UIControl的addTarget:方法
    [datePicker addTarget:self action:@selector(dateChange:) forControlEvents:UIControlEventValueChanged];

    _datePicker = datePicker;

    // inputView:用来自定义文本框的键盘
    // set while first responder, will not take effect until reloadInputViews is called.
    // 局部变量+弱引用 作为输入控件后才能保存对象
    _birthdayField.inputView = datePicker;
}

// 就是给生日文本框赋值
- (void)dateChange:(UIDatePicker *)picker
{
    NSDateFormatter *dateFmt = [[NSDateFormatter alloc] init];
    dateFmt.dateFormat = @"yyyy-MM-dd";

    // 给生日文本框赋值
    _birthdayField.text = [dateFmt stringFromDate:picker.date];
}

// 文本框开始编辑的时候调用
- (void)textFieldDidBeginEditing:(UITextField *)textField
{
    if (textField == _birthdayField) { // 生日键盘
        [self dateChange:_datePicker];
    }
}

```
