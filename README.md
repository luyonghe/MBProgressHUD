


#MBProgressHUD 源码分析
**功能介绍**  

## 1.1 MBProgressHUD
MBProgressHUD 是基于苹果的Foundation.framework 、UIKit.framework和CoreGraphics.framework 框架。


提供了以下功能:
* 提供了很多种样式的提示框
* 提供了带动画的进度提示框
* 提供了页面或者数据加载时候的带动画等待提示框

MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.view animated:YES];
hud.mode = MBProgressHUDModeAnnularDeterminate;

其中hud.model 中的model 是个枚举类型，指提示框的样式，有以下几种类型
typedef enum {
/** 默认模式,使用系统自带的指示器 ,不能显示进度,只能不停地转呀转*/
MBProgressHUDModeIndeterminate,
/** 用饼图显示进度 */
MBProgressHUDModeDeterminate,
/** 进度条 */
MBProgressHUDModeDeterminateHorizontalBar,
/** 圆环 */
MBProgressHUDModeAnnularDeterminate,
/** 自定义视图 */
MBProgressHUDModeCustomView,
/** 只显示文字 */
MBProgressHUDModeText
} MBProgressHUDMode;


##1.2 MBProgressHUD等待数据加载的最基本提示框


###1.2.1不带标题的提示框

![](https://github.com/luyonghe/MBProgressHUD/blob/master/pic/1.png)


￼
[MBProgressHUD showHUDAddedTo:self.view animated:YES];
dispatch_async(dispatch_get_global_queue( DISPATCH_QUEUE_PRIORITY_LOW, 0), ^{
// Do something...
dispatch_async(dispatch_get_main_queue(), ^{
[MBProgressHUD hideHUDForView:self.view animated:YES];
});
});
期中self.view 是指提示框所属的父级页面，为了避免在某些特殊的情景和页面导致提示框突然消失，可以把self.view 换成UIWindow

###1.2.2带一个标题的提示框

![](https://github.com/luyonghe/MBProgressHUD/blob/master/pic/2.png)

￼
MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view animated:YES];

// Set the label text.
hud.label.text = NSLocalizedString(@"Loading...", @"HUD loading title");
// You can also adjust other label properties if needed.
// hud.label.font = [UIFont italicSystemFontOfSize:16.f];

dispatch_async(dispatch_get_global_queue(QOS_CLASS_USER_INITIATED, 0), ^{
[self doSomeWork];
dispatch_async(dispatch_get_main_queue(), ^{
[hud hideAnimated:YES];
});
});

###1.2.3带一个标题和标题描述的提示框

![](https://github.com/luyonghe/MBProgressHUD/blob/master/pic/3.png)

￼
MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view animated:YES];

// Set the label text.
hud.label.text = NSLocalizedString(@"Loading...", @"HUD loading title");
// Set the details label text. Let's make it multiline this time.
hud.detailsLabel.text = NSLocalizedString(@"Parsing data\n(1/1)", @"HUD title");

dispatch_async(dispatch_get_global_queue(QOS_CLASS_USER_INITIATED, 0), ^{
[self doSomeWork];
dispatch_async(dispatch_get_main_queue(), ^{
[hud hideAnimated:YES];
});
});



###1.2.4 MBProgressHUD带有进度条的提示框

![](https://github.com/luyonghe/MBProgressHUD/blob/master/pic/4.png)


MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.view animated:YES];
hud.mode = MBProgressHUDModeAnnularDeterminate;
hud.labelText = @"Loading";
[self doSomethingInBackgroundWithProgressCallback:^(float progress) {
hud.progress = progress; //当前的进度
} completionCallback:^{
[hud hide:YES];
}];



另一种写法是创建一个NSProgress对象来更新进度提示框的进度

MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.view animated:YES];
hud.mode = MBProgressHUDModeAnnularDeterminate;
hud.labelText = @"Loading";
NSProgress *progress = [self doSomethingInBackgroundCompletion:^{
[hud hide:YES];
}];
hud.progressObject = progress;


###1.2.5 MBProgressHUD只显示文字的提示框

￼![](https://github.com/luyonghe/MBProgressHUD/blob/master/pic/5.png)

MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view animated:YES];

// Set the text mode to show only text.
hud.mode = MBProgressHUDModeText;
hud.label.text = NSLocalizedString(@"Message here!", @"HUD message title");
// Move to bottm center.
hud.offset = CGPointMake(0.f, MBProgressMaxOffset);
[hud hideAnimated:YES afterDelay:3.f];

###1.2.6 MBProgressHUD圆环提示框

￼![](https://github.com/luyonghe/MBProgressHUD/blob/master/pic/6.png)
￼
￼
MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view animated:YES];

// Set the annular determinate mode to show task progress.
hud.mode = MBProgressHUDModeAnnularDeterminate;
hud.label.text = NSLocalizedString(@"Loading...", @"HUD loading title");

dispatch_async(dispatch_get_global_queue(QOS_CLASS_USER_INITIATED, 0), ^{
// Do something useful in the background and update the HUD periodically.
[self doSomeWorkWithProgress];
dispatch_async(dispatch_get_main_queue(), ^{
[hud hideAnimated:YES];
});
});


###1.2.7 MBProgressHUD带圆环提示框类型2
MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view animated:YES];

// Set the determinate mode to show task progress.
hud.mode = MBProgressHUDModeDeterminate;
hud.label.text = NSLocalizedString(@"Loading...", @"HUD loading title");

dispatch_async(dispatch_get_global_queue(QOS_CLASS_USER_INITIATED, 0), ^{
// Do something useful in the background and update the HUD periodically.
[self doSomeWorkWithProgress];
dispatch_async(dispatch_get_main_queue(), ^{
[hud hideAnimated:YES];
});
});

￼![](https://github.com/luyonghe/MBProgressHUD/blob/master/pic/7.png)
￼

###1.2.8 MBProgressHUD带按钮的圆环提示框

￼![](https://github.com/luyonghe/MBProgressHUD/blob/master/pic/8.png)

￼
MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view animated:YES];

// Set the determinate mode to show task progress.
hud.mode = MBProgressHUDModeDeterminate;
hud.label.text = NSLocalizedString(@"Loading...", @"HUD loading title");

// Configure the button.
[hud.button setTitle:NSLocalizedString(@"Cancel", @"HUD cancel button title") forState:UIControlStateNormal];
[hud.button addTarget:self action:@selector(cancelWork:) forControlEvents:UIControlEventTouchUpInside];

dispatch_async(dispatch_get_global_queue(QOS_CLASS_USER_INITIATED, 0), ^{
// Do something useful in the background and update the HUD periodically.
[self doSomeWorkWithProgress];
dispatch_async(dispatch_get_main_queue(), ^{
[hud hideAnimated:YES];
});
});

###1.2.9 MBProgressHUD自定义提示框

￼![](https://github.com/luyonghe/MBProgressHUD/blob/master/pic/9.png)

￼
MBProgressHUD *hud = [MBProgressHUD showHUDAddedTo:self.navigationController.view animated:YES];

// Set the custom view mode to show any view.
hud.mode = MBProgressHUDModeCustomView;
// Set an image view with a checkmark.
UIImage *image = [[UIImage imageNamed:@"Checkmark"] imageWithRenderingMode:UIImageRenderingModeAlwaysTemplate];
hud.customView = [[UIImageView alloc] initWithImage:image];
// Looks a bit nicer if we make it square.
hud.square = YES;
// Optional label text.
hud.label.text = NSLocalizedString(@"Done", @"HUD done title");

[hud hideAnimated:YES afterDelay:3.f];

1.7MBProgressHUD混合了多种类型的提示框
MBProgressHUD *hud = [MBProgressHUD HUDForView:self.navigationController.view];
// Indeterminate mode
sleep(2);
// Switch to determinate mode
dispatch_async(dispatch_get_main_queue(), ^{
hud.mode = MBProgressHUDModeDeterminate;
hud.label.text = NSLocalizedString(@"Loading...", @"HUD loading title");
});
float progress = 0.0f;
while (progress < 1.0f) {
progress += 0.01f;
dispatch_async(dispatch_get_main_queue(), ^{
hud.progress = progress;
});
usleep(50000);
}
// Back to indeterminate mode
dispatch_async(dispatch_get_main_queue(), ^{
hud.mode = MBProgressHUDModeIndeterminate;
hud.label.text = NSLocalizedString(@"Cleaning up...", @"HUD cleanining up title");
});
sleep(2);
dispatch_sync(dispatch_get_main_queue(), ^{
UIImage *image = [[UIImage imageNamed:@"Checkmark"] imageWithRenderingMode:UIImageRenderingModeAlwaysTemplate];
UIImageView *imageView = [[UIImageView alloc] initWithImage:image];
hud.customView = imageView;
hud.mode = MBProgressHUDModeCustomView;
hud.label.text = NSLocalizedString(@"Completed", @"HUD completed title");
});
sleep(2);




##2. 结构设计

￼￼![](https://github.com/luyonghe/MBProgressHUD/blob/master/pic/10.png)

2.1相关属性

1、//代理，用于接收HUD提示框状态变化的通知，代理方法是
@property (weak, nonatomic) id<MBProgressHUDDelegate> delegate; 
- (void)hudWasHidden:(MBProgressHUD *)hud;当提示框隐藏的时候调用

2、// 当HUD提示框隐藏完成后调用
@property (copy, nullable) MBProgressHUDCompletionBlock completionBlock;

3、//show函数触发到显示HUD的时间段
@property (assign, nonatomic) NSTimeInterval graceTime; 

4、//HUD显示的最短时间，比如在调用show 方法后立即调用hide 隐藏，
为了避免提示框一闪而过，HUD停留的最短的时间
@property (assign, nonatomic) NSTimeInterval minShowTime; 

5、// HUD提示框隐藏时候，是否直接从父窗体上面移除，默认NO不移除，
设置YES 则HUD提示框隐藏时候直接从父窗体移除
@property (assign, nonatomic) BOOL removeFromSuperViewOnHide; 

6、//HUD提示框显示的类型
@property (assign, nonatomic) MBProgressHUDMode mode; 

7、// 文本内容，菊花指示器，进度条颜色，iOS后属性带UI_APPEARANCE_SELECTOR 可以统一设置全局作用：
只在添加时作用一次，添加结束后，以后设置便不再改变.
@property (strong, nonatomic, nullable) UIColor *contentColor UI_APPEARANCE_SELECTOR;

8、//提示框展示的动画类型
@property (assign, nonatomic) MBProgressHUDAnimation animationType UI_APPEARANCE_SELECTOR; 

9、  // 提示框出现在父空间中的位置
@property (assign, nonatomic) CGPoint offset UI_APPEARANCE_SELECTOR;

10、// HUD 元素到 HUD 边缘的距离，默认是 20.f
@property (assign, nonatomic) CGFloat margin UI_APPEARANCE_SELECTOR; 

11、// 提示框最小size
@property (assign, nonatomic) CGSize minSize UI_APPEARANCE_SELECTOR; 

12、// 是否强制 HUD 背景框宽高相等
@property (assign, nonatomic, getter = isSquare) BOOL square UI_APPEARANCE_SELECTOR;  

13、//进度指示器的进度 0到1，默认是0
@property (assign, nonatomic) float progress; 

14、 //进度指示器进度信息数据模块
@property (strong, nonatomic, nullable) NSProgress *progressObject; 

15、//HUD中间提示框的背景view，里面包含了 labels and indicator
@property (strong, nonatomic, readonly) MBBackgroundView *bezelView;

17、//提示框所属的整个屏幕的背景view
@property (strong, nonatomic, readonly) MBBackgroundView *backgroundView;  

18、//HUD提示框自定义的view，（指示器视图或label视图等等） 
@property (strong, nonatomic, nullable) UIView *customView; 

19、// 标题文本
@property (strong, nonatomic, readonly) UILabel *label; 

20、// 详情文本
@property (strong, nonatomic, readonly) UILabel *detailsLabel; 

21、// hud 窗口还可以加入button，添加事件
@property (strong, nonatomic, readonly) UIButton *button; 

22、 MBRoundProgressView 相关属性
(属于hud提示框指示器中的一种)

/// 进度条 (0.0 到 1.0)
@property (nonatomic, assign) float progress;

/// 进度条颜色
@property (nonatomic, strong) UIColor *progressColor;

/// 进度条指示器的颜色
@property (nonatomic, strong) UIColor *progressTintColor;

/// 进度条指示器的背景颜色，只适用在 iOS7 以上，默认为半透明的白色 (透明度 0.1)
@property (nonatomic, strong) UIColor *backgroundTintColor;

/// 显示模式，NO = 圆形；YES = 环形。默认是 NO
@property (nonatomic, assign, getter = isAnnular) BOOL annular;

23、 MBBarProgressView 相关属性
/// 进度条 (0.0 到 1.0)
@property (nonatomic, assign) float progress;

/// 进度条边界线的颜色，默认是白色
@property (nonatomic, strong) UIColor *lineColor;

/// 进度条背景色，默认是透明
@property (nonatomic, strong) UIColor *progressRemainingColor;

/// 进度条颜色
@property (nonatomic, strong) UIColor *progressColor;


23、 MBBackgroundView 相关属性
/// 背景图层样式，有两种，iOS7 或者以上版本默认风格是MBProgressHUDBackgroundStyleBlur，其他为MBProgressHUDBackgroundStyleSolidColor，由于 iOS7 不支持 UIVisualEffectView，所以在 iOS7 和更高版本中会有所不同
@property (nonatomic) MBProgressHUDBackgroundStyle style;

/// 背景颜色，由于 iOS7 不支持 UIVisualEffectView，所以在 iOS7 和更高版本中会有所不同
@property (nonatomic, strong) UIColor *color;



###3 MBProgressHUD 中的一些方法
3.1 类方法
/// 创建一个 HUD 窗口，并把它显示在 view 上，还可以设置是否有动画
+ (instancetype)showHUDAddedTo:(UIView *)view animated:(BOOL)animated;

/// 找到最上层的 HUD subview 并把它隐藏，成功为YES、其他情况为 NO
+ (BOOL)hideHUDForView:(UIView *)view animated:(BOOL)animated;

/// 返回最上层的 HUD subview
+ (nullable MBProgressHUD *)HUDForView:(UIView *)view;
这三个类方法中，常用的是第一个函数+ (instancetype)showHUDAddedTo:(UIView *)view animated:(BOOL)animated;直接创建 HUD，并把它显示在 view 上，用起来极其方便
1.52 对象方法
/// 以view为基准创建初始化一个HUD对象，为HUD的初始化构造函数
- (instancetype)initWithView:(UIView *)view;

/// 显示HUD控件，此函数应该在主线程中调用
- (void)showAnimated:(BOOL)animated;

/// 隐藏HUD控件，animated控制是否显示动画。对应于- (void)showAnimated:(BOOL)animated;
- (void)hideAnimated:(BOOL)animated;

/// 在delay时间之后隐藏HUD，animated控制显示动画与否，delay控制延迟时间
- (void)hideAnimated:(BOOL)animated afterDelay:(NSTimeInterval)delay;
这几个对象方法中，常用的也有两个- (void)hideAnimated:(BOOL)animated;和- (void)hideAnimated:(BOOL)animated afterDelay:(NSTimeInterval)delay;

