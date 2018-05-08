## 屏幕适配的发展历史
- iPhone3GS\iPhone4
    - 没有屏幕适配可言
    - 全部用frame、bounds、center进行布局
    - 很多这样的现象：坐标值、宽度高度值全部写死
```objc
UIButton *btn1 = [[UIButton alloc] init];
btn1.frame = CGRectMake(0, 0, 320 - b, 480 - c);
```
- iPad出现、iPhone横屏
    - 出现Autoresizing技术
        - 让横竖屏适配相对简单
        - 让子控件可以跟随父控件的行为自动发生相应的变化
        - 前提是：关闭Autolayout功能
        - 局限性
            - 只能解决子控件跟父控件的相对关系问题
            - 不能解决兄弟控件的相对关系问题

- iOS 6.0（Xcode4）开始
    - 出现了Autolayout技术
    - 从Xcode5.0(iOS 7.0)开始，开始流行`Autolayout`

## 什么是适配？
- 适应、兼容各种不同的情况

- 移动开发中，适配的常见种类
系统适配
针对不同版本的操作系统进行适配

- 屏幕适配
针对不同大小的屏幕尺寸进行适配
iPhone的尺寸
3.5inch、4.0inch、4.7inch、5.5inch、
iPad的尺寸
7.9inch、9.7inch

- 屏幕方向 竖屏 横屏

- 设备	        尺寸	        像素            点<br>
iPhone \ iPhone 3G \ iPhone 3GS	3.5 inch	320 x 480	320 x 480
iPhone 4 \ iPhone 4S	3.5 inch	640 x 960	320 x 480
iPhone 5 \ iPhone 5C \ iPhone 5S	4.0 inch	640 x 1136	320 x 568
iPhone6	4.7 inch	750 x 1334	375 x 667
iPhone6 plus	5.5 inch	1242 x 2208	414 x 736
iPad \ iPad2	9.7 inch	768 x 1024	768 x 1024
iPad 3(The new iPad) \ iPad4 \ iPad Air	9.7 inch	1536 x 2048	768 x 1024
iPad Mini	7.9 inch	768 x 1024	768 x 1024
iPad Mini 2(iPad Mini with retina display)	7.9 inch	1536 x 2048	768 x 1024

## 什么是Autolayout
- Autolayout是一种“自动布局”技术，专门用来布局UI界面的
- Autolayout自iOS 6开始引入，由于Xcode 4的不给力，当时并没有得到很大推广
- 自iOS 7（Xcode 5）开始，Autolayout的开发效率得到很大的提升
- 苹果官方也推荐开发者尽量使用`Autolayout`来布局UI界面
- `Autolayout`能很轻松地解决屏幕适配的问题
- `Autolayout`的2个核心概念 `参照` `约束`<br>
- Autolayout常用面板01-约束处理<br>
![](/assets/WX20180130-112400.png)
- Autolayout常用面板02-相对<br>
![](/assets/WX20180130-113424.png)
- Autolayout常用面板03-对齐<br>
![](/assets/WX20180130-113909.png)
- Autolayout的警告和错误<br>
    - 警告
      控件的frame不匹配所添加的约束, 比如
      比如约束控件的宽度为100, 而控件现在的宽度是110
    - 错误
      缺乏必要的约束, 比如
      只约束了宽度和高度, 没有约束具体的位置
      两个约束冲突, 比如
      1个约束控件的宽度为100, 1个约束控件的宽度为110


## 使用代码实现Autolayout的方法1
- 创建约束

```objc
+(id)constraintWithItem:(id)view1
attribute:(NSLayoutAttribute)attr1
relatedBy:(NSLayoutRelation)relation
toItem:(id)view2
attribute:(NSLayoutAttribute)attr2
multiplier:(CGFloat)multiplier
constant:(CGFloat)c;
* view1 ：要约束的控件
* attr1 ：约束的类型（做怎样的约束）
* relation ：与参照控件之间的关系
* view2 ：参照的控件
* attr2 ：约束的类型（做怎样的约束）
* multiplier ：乘数
* c ：常量
```

- 添加约束

```objc
- (void)addConstraint:(NSLayoutConstraint *)constraint;
- (void)addConstraints:(NSArray *)constraints;
```

- 注意
    - 一定要在拥有父控件之后再添加约束
    - 关闭Autoresizing功能
    ```objc
    view.translatesAutoresizingMaskIntoConstraints = NO;
    ```

- 自动布局的核心计算公式
```objc
obj1.property1 =（obj2.property2 * multiplier）+ constant value
```
- 添加约束的规则（1）<br>
在创建约束之后，需要将其添加到作用的view上<br>
在添加时要注意目标view需要遵循以下规则：<br>
    - 对于两个同层级view之间的约束关系，添加到它们的父view
![](/assets/WX20180130-115331.png)
    - 对于两个不同层级view之间的约束关系，添加到他们最近的共同父view上<br>
![](/assets/WX20180130-115437.png)
    - 对于有层次关系的两个view之间的约束关系，添加到层次较高的父view上<br>
![](/assets/WX20180130-115646.png)

## 使用代码实现Autolayout的方法2 - VFL
- 什么是VFL语言
    - VFL全称是Visual Format Language，翻译过来是“可视化格式语言”
    - VFL是苹果公司为了简化Autolayout的编码而推出的抽象语言
![](/assets/WX20180130-115855.png)

- VFL示例

```objc
H:[cancelButton(72)]-12-[acceptButton(50)]
canelButton宽72，acceptButton宽50，它们之间间距12

H:[wideView(>=60@700)]
wideView宽度大于等于60point，该约束条件优先级为700（优先级最大值为1000，优先级越高的约束越先被满足）

V:[redBox][yellowBox(==redBox)]
竖直方向上，先有一个redBox，其下方紧接一个高度等于redBox高度的yellowBox

H:|-10-[Find]-[FindNext]-[FindField(>=20)]-|
水平方向上，Find距离父view左边缘默认间隔宽度，之后是FindNext距离Find间隔默认宽度；再之后是宽度不小于20的FindField，它和FindNext以及父view右边缘的间距都是默认宽度。（竖线“|” 表示superview的边缘）
```
- `有了Autolayout的UILabel`
    - 在没有Autolayout之前，UILabel的文字内容总是居中显示，导致顶部和底部会有一大片空缺区域<br>
![](/assets/WX20180130-130754.png)
    - 有Autolayout之后，UILabel的bounds默认会自动包住所有的文字内容，顶部和底部不再会有空缺区域<br>
![](/assets/WX20180130-130818.png)

- 基于Autolayout的动画
    - 在修改了约束之后，只要执行下面代码，就能做动画效果
    ```objc
    [UIView animateWithDuration:1.0 animations:^{
        // 强制更新
        [添加了约束的view layoutIfNeeded];
    }];
    ```

- 使用VFL创建约束数组

```objc
+ (NSArray *)constraintsWithVisualFormat:(NSString *)format
options:(NSLayoutFormatOptions)opts
metrics:(NSDictionary *)metrics
views:(NSDictionary *)views;
* format ：VFL语句
* opts ：约束类型
* metrics ：VFL语句中用到的具体数值
* views ：VFL语句中用到的控件
```

- 使用下面的宏来自动生成views和metrics参数

```objc
NSDictionaryOfVariableBindings(...)
```

## 使用代码实现Autolayout的方法3 - Masonry
- Masonry
    - 目前最流行的Autolayout第三方框架
    - 用优雅的代码方式编写Autolayout
    - 省去了苹果官方恶心的Autolayout代码
    - 大大提高了开发效率
    - 框架地址：
    - https://github.com/SnapKit/Masonry

- Masonry使用步骤
    - 添加Masonry文件夹的所有源代码到项目中
    - 添加2个宏、导入主头文件
    - 默认情况下
    - mas_equalTo有自动包装功能，比如自动将20包装为@20
    - equalTo没有自动包装功能
    - 如果添加了下面的宏，那么mas_equalTo和equalTo就没有区别
    ```objc
    #define MAS_SHORTHAND_GLOBALS
    // 注意：这个宏一定要添加到#import "Masonry.h"前面
    ```
    - 默认情况下
width是make对象的一个属性，用来添加宽度约束用的，表示对宽度进行约束
    - mas_width是一个属性值，用来当做equalTo的参数，表示某个控件的宽度属性
    - 如果添加了下面的宏，mas_width也可以写成width
    ```objc
    #define MAS_SHORTHAND
    mas_height、mas_centerX以此类推
    // 注意：这个宏一定要添加到#import "Masonry.h"前面
    ```

- 可有可无的用法
    - 以下方法都仅仅是为了提高可读性，可有可无

    ```objc
        - (MASConstraint *)with {
            return self;
        }

        - (MASConstraint *)and {
            return self;
        }
    ```

- 添加约束的方法

```objc
// 这个方法只会添加新的约束
 [view makeConstraints:^(MASConstraintMaker *make) {

 }];

// 这个方法会将以前的所有约束删掉，添加新的约束
 [view remakeConstraints:^(MASConstraintMaker *make) {

 }];

 // 这个方法将会覆盖以前的某些特定的约束
 [view updateConstraints:^(MASConstraintMaker *make) {

 }];
```

- 约束的类型
```objc
1.尺寸：width\height\size
2.边界：left\leading\right\trailing\top\bottom
3.中心点：center\centerX\centerY
4.边界：edges
```