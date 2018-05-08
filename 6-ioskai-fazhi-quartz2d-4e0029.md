## 今天给同学们全面的讲解Quartz2D以及Quartz2D的相关实战例子：例子请参考我个人CSDN之前所发的有关Quartz2D的项目的博文,那么废话不多说,直接上代码~
http://blog.csdn.net/ZZ_IOSdeveloper
- Quartz2D简介
- Quartz2D能完成的工作
- Quartz2D在iOS开发中的价值
- Quartz2D必须掌握(`重点`)
- 图形上下文&图形上下文栈&常用函数
- Quartz2D绘图演练(画线\图形\进度条\饼块\柱状图......)
- 手势解锁
- 矩阵操作
- Quartz2D的内存管理

## Quartz2D简介
- 什么是Quartz2D?二维的绘图引擎
- 什么是二维?平面
- 什么是引擎?经包装的函数库，方便开发者使用。也就是说苹果帮我们封装了一套绘图的函数库
- 同时支持iOS和Mac系统什么意思?用Quartz2D写的同一份代码，既可以运行在iphone上又可以运行在mac上，可以跨平台开发。
- 开发中比较常用的是截屏/裁剪/自定义UI控件。
- Quartz2D在iOS开发中的价值就是自定义UI控件。
- 图形上下文的数据类型和作用。
- 有多少种上下文。
- 自定义控件的步骤。
- 为什么要实现drawRect:方法，因为只有在drawRect:方法中才能获取到上下文

## Quartz2D能完成的工作
- 绘制图形 : 线条\三角形\矩形\圆\弧等
- 绘制文字
- 绘制\生成图片(图像)
- 读取\生成PDF
- 截图\裁剪图片
- 自定义UI控件
- ......

## Quartz2D在iOS开发中的价值
- 为了便于搭建美观的UI界面，iOS提供了UIKit框架，里面有各种各样的UI控件
UILabel：显示文字
UIImageView：显示图片
UIButton：同时显示图片和文字（能点击）
… …
- 利用UIKit框架提供的控件，拼拼凑凑，能搭建和现实一些简单、常见的UI界面
- 但是，有些UI界面极其复杂、而且比较个性化，用普通的UI控件无法实现，这时可以利用Quartz2D技术将控件内部的结构画出来，自定义控件的样子
- 其实，iOS中大部分控件的内容都是通过Quartz2D画出来的
- 因此，Quartz2D在iOS开发中很重要的一个价值是：自定义view（`自定义UI控件`）

## Quartz2D必须掌握
- drawRect:方法的使用
- 常见图形的绘制：线条、多边形、圆
- 绘图状态的设置：文字颜色、线宽等
- 图形上下文状态的保存与恢复（图形上下文栈）
- 图片裁剪
- 截图

## drawRect:中取得的上下文
- 在drawRect:方法中取得上下文后，就可以绘制东西到view上
- View内部有个layer（图层）属性，drawRect:方法中取得的是一个Layer Graphics Context，因此，绘制的东西其实是绘制到view的layer上去了
- View之所以能显示东西，完全是因为它内部的layer

## 图形上下文
- 图形上下文（Graphics Context）：是一个CGContextRef类型的数据
- 图形上下文的作用:
    - 保存绘图信息、绘图状态决定绘制的输出目标（绘制到什么地方去？）（输出目标可以是PDF文件、Bitmap或者显示器的窗口上）
    ![](/assets/WX20180207-181156.png)
- 相同的一套绘图序列，指定不同的Graphics Context，就可将相同的图像绘制到不同的目标上
- Quartz2D提供了以下几种类型的Graphics Context：
    - Bitmap Graphics Context
    - PDF Graphics Context
    - Window Graphics Context
    - Layer Graphics Context
    - Printer Graphics Context
    ![](/assets/WX20180207-181423.png)
    
## 常用拼接路径函数
```objc
新建一个起点
void CGContextMoveToPoint(CGContextRef c, CGFloat x, CGFloat y)

添加新的线段到某个点
void CGContextAddLineToPoint(CGContextRef c, CGFloat x, CGFloat y)

添加一个矩形
void CGContextAddRect(CGContextRef c, CGRect rect)

添加一个椭圆
void CGContextAddEllipseInRect(CGContextRef context, CGRect rect)

添加一个圆弧
void CGContextAddArc(CGContextRef c, CGFloat x, CGFloat y,
  CGFloat radius, CGFloat startAngle, CGFloat endAngle, int clockwise)
```

## 常用绘制路径函数
```objc
Mode参数决定绘制的模式
void CGContextDrawPath(CGContextRef c, CGPathDrawingMode mode)

绘制空心路径
void CGContextStrokePath(CGContextRef c)

绘制实心路径
void CGContextFillPath(CGContextRef c)

提示：一般以CGContextDraw、CGContextStroke、CGContextFill开头的函数，都是用来绘制路径的
```

## 图形上下文栈
```objc
将当前的上下文copy一份,保存到栈顶(那个栈叫做”图形上下文栈”)
void CGContextSaveGState(CGContextRef c)

将栈顶的上下文出栈,替换掉当前的上下文
void CGContextRestoreGState(CGContextRef c)
```

## Quartz2D绘图演练
- 在哪画? storyboard拖一个view，在这个view里面画一些东西。
- 自定义view:需要绘图，就必须重写drawRect:方法
- ZZLineView:绘制线段
- drawRect:方法自动生成，意味着什么?这个方法很重要。
    - 什么时候调用:视图要显示的时候，才会调用,viewDidLoad后才会调用，因为那时候还没显示视图。
    - 作用:用来绘图

## 画线
```objc
1> 获取图形上下文
CG:表示这个类在CoreGraphics框架里  Ref:引用目前学的上下文都跟UIGraphics有关，想获取图形上下文，首先敲UIGraphics。
2> 拼接路径：一般开发中用贝塞尔路径，里面封装了很多东西，可以帮我画一些基本的线段，矩形，圆等等。
* 创建贝塞尔路径
* 起点:moveToPoint
* 终点:addLineToPoint
3> 把路径添加到上下文
CGPath转换：UIKit框架转CoreGraphics直接CGPath就能转
4> 把上下文渲染到视图，图形上下文本身不具备显示功能。
* 首先获取图形上下文，然后描述路径，把路径添加到上下文，渲染到视图，图形上下文相当于一个内存缓存区，在内存里面操作是最快的，比直接在界面操作快多了。
5> 在添加一根线
直接addLineToPoint，因为路径是拼接的，默认下一条线的起点是上一条线的终点。
6> 画两跟不连接的线
* 第二次画的时候，重新设置起点，然后画线。一个路径可以包含多条线段。
* 新创建一个路径，添加到上下文。开发中建议使用这种，比较容易控制每根线。
7> 设置绘图状态
* 线段怎么加粗。
* 绘图状态调用顺序：只要在渲染之前就好了，在渲染的时候才会去看绘图的最终状态。
8> 画曲线
* 分析：3个点，起点，终点，控制点。
```

## 绘制图形
```objc
triangle三角形
1> 关闭路径closePath：从路径的终点连接到起点
2> 填充路径CGContextFillPath：有了封闭的路径就能填充。
3> 设置填充颜色 [[UIColor blueColor] setFill];
4> 设置描边颜色 [[UIColor redColor] setStroke];
5> 不显示描边颜色，为什么?没有设置线宽
6> 设置线宽，还是不显示，为什么?因为绘制路径不对。
7> 即填充又描边CGContextDrawPath:kCGPathFillStroke。

rectangle矩形
circle圆：为什么传入矩形，因为圆内切与矩形
arc圆弧
1> 圆弧属于圆的一部分，因此先要有圆，才有弧。
2> 圆需要起点吗？画线需要，圆也不另外。
3> 起点在哪? 圆心右边
4> 画圆弧还需要起始角度，结束角度，方向，角度必须是弧度

Quarter 1/4圆
画个1/4圆弧，Stroke
填充路径，Fill
必须有封闭路径才能填充，没有封闭路径，系统会自动关闭路径，再去填充
画线连接圆心，自动关闭路径。
```    

## 基本图形绘制
```objc
绘制线段(Line)：绘制直线->在添加一条直线->绘制两条直线(路径填充、设置路径属性，颜色，线宽等)->曲线->分析
绘制图形(Shape)：绘制三角形(超人内裤)->矩形->圆->圆弧->1/4圆
```

## 重绘-下载进度条(setNeedsDisplay)
```objc
* 分析有几个控件：UISliderView,自定义view用来画图,UILabel
* 分析思路：滑动UISliderView的时候，把UISliderView的值传给自定义view,改变的显示。
* 怎么监听UISliderView，valueChange事件
* 自定义view搞一个属性接收滑动的值
* 接下来先搞label,因为他比较简单，做东西，先从简单的着手。
* 懒加载label,位置居中，文字居中
* label怎么显示?重写progress的set方法,有数据就展示在label上。
* 绘制圆弧，从哪开始画圆弧。
* 每次转多少°？ 角度 = 进度 * M_PI * 2
* startAngle = -M_PI_2
* endAngle = -M_PI_2 + 进度 * M_PI * 2
* 不会及时更新视图显示?为什么，因为drawRect只会在视图显示的时候调用一次。
* 怎么重绘？
* 手动调用drawRect方法，不行，因为你自己不能创建上下文，必须系统调用来调用
* setNeedsDisplay：在view上做一个重绘的标记，在下一次绘图的周期来临，就会先创建好上下文，然后自动调用drawRect重绘。
```

## 绘制饼图
```objc
* 分析 -> 找规律 -> 得出startA,endA,angle的结论
* startA = endA angle = 比例 * 总度数360° endA = startA + angle
* angle = 自己 / 100.0 * 360
* 一个一个扇形画
```

## 绘制柱状图
```objc
* 分析 -> 找规律 -> 得出w,h,y,x的结论
* w = viewW / (2 * count - 1)
* h = viewH * 比例
```

## UIKit封装绘图方法演练(不需要上下文)
- 绘制文本
- 绘制矩形
- 绘制图像
- 模仿UIImageView/雪花(定时器)
- 图片水印
    - 图片水印作用：防止他人盗取图片，加一些Logo，生成一张新的图片。
    - 怎么生成新的图片?和绘图一样的，需要拿到上下文做事情，这里也需要拿到上下文，生成一个新的图片。
    - 什么上下文？位图上下文，在这个上下文画东西，就能输出到新的图片上。
    - 怎么获取？之前用的都是图层上下文，系统会自动创建，但是我们位图上下文，需要我们手动创建
    - 总结：只要不和view有关系的上下文，都需要我们手动创建。
    - 在哪获取图像上下文，viewDidLoad, 不需要拿到系统创建的图层上下文，没必要在drawRect方法里写,直接viewDidLoad就行了。
    - 怎么创建图像上下文了？之前说过跟上下文有关的以什么开头，UIGraphics
    - UIGraphicsBeginImageContextWithOptions:看注释,create bitmap，创建一个位图上下文,而且这种方法得到的图片最清晰。
        - 解释参数（size:新图片尺寸 opaque: YES:不透明 NO:透明 scale:0.0 不伸缩）
        - 绘制内容(图片，文字)
        - 获取图片：把位图上下文的内容生成一个图片给你。 
        - 关闭上下文，不关闭一直占用着内存。
        - 显示UIImageView上
        - 保存图片，写到文件，UIImage不能写，需要转换成NSData二进制数据
        - UIImageJPEGRepresentation:可以设置图片质量
        - UIImagePNGRepresentation：把图片转换成png格式的二进制数据,png格式默认是最高清的。
    - 写到桌面

```objc
有时候，在手机客户端app中也需要用到水印技术

比如，用户拍完照片后，可以在照片上打个水印，标识这个图片是属于哪个用户的

实现方式：利用Quartz2D，将水印（文字、LOGO）画到图片的右下角

核心代码
开启一个基于位图的图形上下文
void     UIGraphicsBeginImageContextWithOptions(CGSize size, BOOL opaque, CGFloat scale)

从上下文中取得图片（UIImage）
UIImage * UIGraphicsGetImageFromCurrentImageContext();

结束基于位图的图形上下文
void UIGraphicsEndImageContext();
```
- 图像裁切 -> 指定裁切区域之后，所有绘图信息都只显示裁切区域内
- 图片裁剪
    - 先设置裁剪区域，把图片画上去，超出裁剪区域的自动裁剪掉。
    - 加载旧图片，根据旧图片，获取上下文尺寸。
    - 上下文的尺寸 = 新图片的尺寸
    - 开启一个多大的上下文?:和图片尺寸一样大，避免压缩图片。如果比图片尺寸小，会压缩图片。
    - 设置裁剪区域：正切于图片的圆
    - 绘制旧图片
    - 获取新图片
    - 关闭上下文
- 带圆环裁剪:在裁剪的图片外边加个小圆环。
    - 先画一个大圆，在设置裁剪区域，把图片画上去，超出裁剪区域的自动裁剪掉。
    - 加载旧图片，根据旧图片，获取上下文尺寸。
    - 确定圆环宽度 borderW
    - 上下文的尺寸 = 新图片的尺寸
    - 确定新的上下文尺寸: newImageW : oldImageW + 2 * borderW  newImageH : oldImageH + 2 * borderW，
    - 绘制大圆：
        - 1.获取上下文 
        - 2.添加路径到上下文 
        - 3.设置大圆的颜色 = 圆环的颜色 
        - 4.渲染
    - 设置裁剪区域,和图片尺寸一样大，只不过，x,y不一样，x=borderW,y=borderW.
    - 绘制旧图片
    - 获取新图片
    - 关闭上下文
    - 抽分类，3个参数，图片名称，圆环宽度，圆环颜色

```objc
核心代码
void CGContextClip(CGContextRef c)
将当前上下所绘制的路径裁剪出来（超出这个裁剪区域的都不能显示）
```

- 屏幕截图:把屏幕的内容截屏生成一张新的图片
    - 通常开发中，都是把控制器的内容截屏，生成新的图片
    - 控制器怎么显示？根据view
    - view怎么显示? 根据layer图层
    - 把layer渲染到位图上下文
    - 注意：图层只能用渲染，图片和文字可以用draw
    - 渲染在哪?新的图片
    - 开启图片上下文,和视图一样的尺寸
    - 写入桌面
    - 抽分类

```objc
核心代码
- (void)renderInContext:(CGContextRef)ctx;
调用某个view的layer的renderInContext:方法即可
```

## 矩阵操作
```objc
利用矩阵操作，能让绘制到上下文中的所有路径一起发生变化
缩放
void CGContextScaleCTM(CGContextRef c, CGFloat sx, CGFloat sy)

旋转
void CGContextRotateCTM(CGContextRef c, CGFloat angle)

平移
void CGContextTranslateCTM(CGContextRef c, CGFloat tx, CGFloat ty)
```
## 手势解锁
    - 分析界面有几个控件：背景：UIImageView 白色圆圈：按钮（点击他，会出现另外一种图片，按钮可以设置不同状态下的图片。）单独视图：(画线是有范围的，当超出view就不能画线了)
    - ZZLoadView:自定义视图，在视图一创建的时候，就添加9个按钮。
    - 在initWithCoder，initWithFrame方法添加按钮。
    - 九宫格布局：
    ```objc
tolcols = 3
计算row,col 按钮的x，y跟col,row有关系，col = i % tolcol row = i / tolcol
计算边距 margin = (view.bounds.size.width - tolcol * btnW) / (tolcol + 1)
btnX = margin + (btnW + margin) * col
btnY = (btnW + margin) * row
    ```
    -  圆的选中
        - 点击按钮就为选中的图片怎么做？监听按钮点击。
        - 不能addTarget: 不能及时显示选中图片。
        - 监听touchBegin,判断点在不在按钮的frame上。
        - touchBegin不调用？原因：事件交给按钮处理，应该把事件交给解锁视图。让按钮不接收事件。
        - 设置按钮不允许交互，2个用处：1.不接收事件 2.取消高亮效果 一举两得
        - 遍历所有按钮，看触摸点在哪个按钮上，就选中谁，CGRectContainsPoint 传入的参数必须是同一个坐标系
        - 实现touchMove方法：因为手指移动的时候，也需要判断点在不在按钮上。
        - 抽方法，因为touchMove的方法里，也需要做同样的事情。
            - 1> pointWithTouches 根据touches集合取出触摸点
            - 2> buttonWithPoint 根据触摸点，获取触摸按钮
    - 圆的连线
        - 哪些需要连线？被选中按钮之间都需要连线，还有一个多余的线
        - 先把选中的按钮全部连线？为什么，因为多余的那根线是从最后一个按钮的圆心开始画，手指移动在哪就画哪。
        - 搞个数组保存下所有选中按钮，在drawRect方法中遍历所有按钮，连线
        - UIBezierPath画线，不需要上下文。
        - 需要多少个UIBezierPath对象？一个，路径都是连续的，不相连的，才需要创建新的UIBezierPath。
        - 遍历数组，描述路径
            - 1> 起点：第一个按钮的圆心
            - 2> 添加一根线到其他按钮的圆心
        - 设置路径的颜色和线宽
        - 什么时候渲染？把所有路径都描述完就，渲染一次就够了。
        - [path stroke] 就能渲染到视图上了。
        - 画多余的那根线，记住手指移动的位置。
        - setNeedDisplay 没有画出来线?因为drawRect只会调用一次，需要每次手指移动的时候，都需要重绘。
        - touchBegin不调用setNeedDisplay?那时候重绘也没用，只有起点。
        - lineJoinStyle：有尖尖的东西，线段连接样式的问题，设置为平的。
        - 已经选中的按钮，不需要再次选中，和画线
        - 手指抬起，取消所有选中按钮，并且清空数组，清空线条
        - drawRect判断下没有选中数组，不需要画线。
        - 如何判断用户是否输入正确?给选中按钮绑定tag,遍历所有选中按钮，把tag拼接成一个字符串。

## Quartz2D的内存管理<br>
```objc
使用含有“Create”或“Copy”的函数创建的对象，使用完后必须释放，否则将导致内存泄露

使用不含有“Create”或“Copy”的函数获取的对象，则不需要释放

如果retain了一个对象，不再使用时，需要将其release掉

可以使用Quartz 2D的函数来指定retain和release一个对象。例如，如果创建了一个CGColorSpace对象，则使用函数CGColorSpaceRetain和CGColorSpaceRelease来retain和release对象。

也可以使用Core Foundation的CFRetain和CFRelease。注意不能传递NULL值给这些函数
```
![](/assets/WX20180207-181156.png)