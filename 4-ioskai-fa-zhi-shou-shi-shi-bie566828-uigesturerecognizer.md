## 各位同学今天给大家带来如下内容那么废话不多说,直接上代码
- 手势识别-UIGestureRecognizer
- 抽屉效果

## 手势识别-UIGestureRecognizer
- 为了完成手势识别，必须借助于手势识别器---UIGestureRecognizer
- 利用UIGestureRecognizer，能轻松识别用户在某个view上面做的一些常见手势

```objc
UIGestureRecognizer是一个抽象类，定义了所有手势的基本行为，使用它的子类才能处理具体的手势
UITapGestureRecognizer(敲击)
UIPinchGestureRecognizer(捏合，用于缩放)
UIPanGestureRecognizer(拖拽)
UISwipeGestureRecognizer(轻扫)
UIRotationGestureRecognizer(旋转)
UILongPressGestureRecognizer(长按)
```
- 每一个手势识别器的用法都差不多，比如UITapGestureRecognizer的使用步骤如下:

```objc
创建手势识别器对象
UITapGestureRecognizer *tap = [[UITapGestureRecognizer alloc] init];

#pragma mark - 设置手势识别器对象的具体属性
// 连续敲击2次
tap.numberOfTapsRequired = 2;

// 需要2根手指一起敲击
tap.numberOfTouchesRequired = 2;

添加手势识别器到对应的view上
[self.iconView addGestureRecognizer:tap];

监听手势的触发
[tap addTarget:self action:@selector(tapIconView:)];
```
- 手势识别的状态<br>
```objc
typedef NS_ENUM(NSInteger, UIGestureRecognizerState) {
    // 没有触摸事件发生，所有手势识别的默认状态
    UIGestureRecognizerStatePossible,
    // 一个手势已经开始但尚未改变或者完成时
    UIGestureRecognizerStateBegan,
    // 手势状态改变
    UIGestureRecognizerStateChanged,
    // 手势完成
    UIGestureRecognizerStateEnded,
    // 手势取消，恢复至Possible状态
    UIGestureRecognizerStateCancelled, 
    // 手势失败，恢复至Possible状态
    UIGestureRecognizerStateFailed,
    // 识别到手势识别
    UIGestureRecognizerStateRecognized = UIGestureRecognizerStateEnded
};
```
- 手势识别状态变化示意图
- ![](/assets/UIGestureRecognizer.png)

- 使用UIImageView原因：之前既能看见图片，又能监听点击的只有UIButton,学了手势，我们的UIImageView也可以。
    - tap(代理：左边不能点，右边能点)
    - longPress(allowableMovement:触发之前，最大的移动范围)
        - 默认调用两次，开始一次，结束一次。
    - swipe:(一个手势只能识别一个方向)
    - 旋转：
        - 基于上一次旋转`注意`:通过transform形变，需要去掉autolayout,才准确
        - 复位：(手势的取值都是相对最原始的位置，我们应该是需要相对上一次，因此每次调用，就复位一下，每次都是从零开始旋转角度)
        - 缩放：复位
- 如何同时支持旋转和缩放？默认不支持多个手指
    - Simultaneously
        - 同时当使用一个手势的时候会调用代理的Simultaneously方法，询问是否支持多个手势
    - pan
        - 获取平移的位置：translationInView
        - 复位：setTranslation:inView: 需要传一个view，因为点的位置跟坐标系有关系，看他是基于哪个坐标系被清空的。

## 抽屉效果
- 简单的滑动效果
    - 监听控制器处理事件方法
        - 获取x轴偏移量
        - 改变主视图的frame
    - 利用KVO做视图切换
        - 往左移动，显示右边，隐藏左边
        - 往右移动，显示左边，隐藏右边
- 复杂的滑动效果
    - （根据手指每移动一点，x轴的偏移量算出当前视图的frame）
    ```objc
    假设x移到320时，y移动到60，算出每移动一点x，移动多少y
    offsetY = offsetX * 60 / 320  手指每移动一点，x轴偏移量多少，y偏移多少
    为了好看，x移动到320，距离上下的高度需要保持一致，而且有一定的比例去缩放他的尺寸。
    怎么根据之前的frame，算出当前的frame,touchMove只能拿到之前的frame.
    当前的高度 = 之前的高度 * 这个比例
    缩放比例：当前的高度/之前的高度  (screenH - 2 * offsetY) / screenH
    当前的宽度也一样求。
    y值，计算比较特殊，不能直接用之前的y,加上offsetY,往左滑动，主视图应该往下走，但是offsetX是负数，导致主视图会往上走。
    y = （screenH - 当前的高度）* 0.5
    getCurrentFrameWithOffsetX
    定位(滑动松开手指的时候，移动到目标点)
    移动到左右目标点，根据偏移量 = 当前目标点的x - 之前视图的x，计算移动到目标点的frame
    还原：当没有移动到目标点，就把主视图还原。
    复位（当主视图不在原始的位置，点击屏幕，恢复原来位置）
    判断手指是否移动，移动的时候就自动定位，不需要手动复位。
    ```