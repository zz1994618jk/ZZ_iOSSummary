## 今天给同学们全面的讲解Quartz2D以及Quartz2D的相关实战例子：例子请参考我个人CSDN之前所发的有关Quartz2D的项目的博文,那么废话不多说,直接上代码~
http://blog.csdn.net/ZZ_IOSdeveloper
- Quartz2D绘图方法名
- NSString字符串的画图写入
- 利用Quartz2D对图片放大缩小的功能(效率低了点)
- 利用Quartz2D绘制图片
- 利用Quartz2D实现逐变颜色填充方法
- Quartz2D使用注意点(注意点)
- Quartz2D Function Description (方法解读)
- 利用Quartz2D绘制动态曲线

## Quartz2D绘图方法名
```objc
CGContextRef context = UIGraphicsGetCurrentContext(); 设置上下文
CGContextMoveToPoint 开始画线
CGContextAddLineToPoint 画直线
CGContextAddEllipseInRect 画一椭圆
CGContextSetLineCap 设置线条终点形状
CGContextSetLineDash 画虚线
CGContextAddRect 画一方框
CGContextStrokeRect 指定矩形
CGContextStrokeRectWithWidth 指定矩形线宽度
CGContextStrokeLineSegments 一些直线
CGContextAddArc 画已曲线 前俩点为中心 中间俩点为起始弧度 最后一数据为0则顺时针画 1则逆时针
CGContextAddArcToPoint(context,0,0, 2, 9, 40);//先画俩条线从point 到 弟1点 ， 从弟1点到弟2点的线  切割里面的圆
CGContextSetShadowWithColor 设置阴影
CGContextSetRGBFillColor 这只填充颜色
CGContextSetRGBStrokeColor 画笔颜色设置
CGContextSetFillColorSpace 颜色空间填充
CGConextSetStrokeColorSpace 颜色空间画笔设置
CGContextFillRect 补充当前填充颜色的rect
CGContextSetAlaha 透明度
CGContextTranslateCTM 改变画布位置
CGContextSetLineWidth 设置线的宽度
CGContextAddRects 画多个线
CGContextAddQuadCurveToPoint 画曲线
CGContextStrokePath 开始绘制图片
CGContextDrawPath 设置绘制模式
CGContextClosePath 封闭当前线路
CGContextTranslateCTM(context, 0, rect.size.height);    CGContextScaleCTM(context, 1.0, -1.0);反转画布
CGContextSetInterpolationQuality 背景内置颜色质量等级
CGImageCreateWithImageInRect 从原图片中取小图
CGColorGetComponents（）返回颜色的各个直 以及透明度 可用只读const float 来接收  是个数组
```

## NSString字符串的画图写入
- NSString本身的画图方法

```objc
// 通过如下方法实现即可
- (CGSize)drawInRect:(CGRect)rect withFont:(UIFont *)font lineBreakMode:(UILineBreakMode)lineBreakMode alignment:(UITextAlignment)alignment;
```

## 利用Quartz2D对图片放大缩小的功能(`效率低了点`)
```objc
UIGraphicsBeginImageContext(newSize);
UIImage* newImage = UIGraphicsGetImageFromCurrentImageContext();
UIGraphicsEndImageContext();
```

## 利用Quartz2D绘制图片
```objc
CGImageRef image＝CGImageRetain(img.CGImage);
CGContextDrawImage(context, CGRectMake(10.0, height -              
100.0, 90.0, 90.0), image);
```

## 利用Quartz2D实现逐变颜色填充方法
```objc
CGContextClip(context);
CGColorSpaceRef rgb = CGColorSpaceCreateDeviceRGB();
CGFloat colors[] = {
   204.0 / 255.0, 224.0 / 255.0, 244.0 / 255.0, 1.00,
   29.0 / 255.0, 156.0 / 255.0, 215.0 / 255.0, 1.00,
   0.0 / 255.0,  50.0 / 255.0, 126.0 / 255.0, 1.00,
};
CGGradientRef gradient = CGGradientCreateWithColorComponents(rgb, colors, NULL, sizeof(colors)/(sizeof(colors[0])*4));
CGColorSpaceRelease(rgb);    
CGContextDrawLinearGradient(context, gradient,CGPointMake    (0.0,0.0) ,CGPointMake(0.0,self.frame.size.height),                    
kCGGradientDrawsBeforeStartLocation);
```
 
## Quartz2D使用注意点(`注意点`)  
- 注:画完图后,必须 
  - 先用CGContextStrokePath来描线,即形状 
  - 后用CGContextFillPath来填充形状内的颜色. 
  - 填充一个路径的时候，路径里面的子路径都是独立填充的。
  - 假如是重叠的路径，决定一个点是否被填充，有两种规则如下:
    - 1> nonzero winding number rule:非零绕数规则，假如一个点被从左到右跨过，计数器+1，从右到左跨过，计数器-1，最后，如果结果是0，那么不填充，如果是非零，那么填充。
    - 2> even-odd rule: 奇偶规则，假如一个点被跨过，那么+1，最后是奇数，那么要被填充，偶数则不填充，和方向没有关系。
 
## Quartz2D Function Description (`方法解读`)
```objc
CGContextEOFillPath 使用奇偶规则填充当前路径
CGContextFillPath 使用非零绕数规则填充当前路径
CGContextFillRect 填充指定的矩形
CGContextFillRects 填充指定的一些矩形
CGContextFillEllipseInRect 填充指定矩形中的椭圆
CGContextDrawPath 两个参数决定填充规则
kCGPathFill 表示用非零绕数规则
kCGPathEOFill 表示用奇偶规则
kCGPathFillStroke 表示填充
kCGPathEOFillStroke 表示描线，不是填充

设置当一个颜色覆盖上另外一个颜色，两个颜色怎么混合默认方式是:
result = (alpha * foreground) + (1 - alpha) * background
CGContextSetBlendMode :设置blend mode.
CGContextSaveGState :保存blend mode.
CGContextRestoreGState:在没有保存之前，用这个函数还原blend mode.
CGContextSetBlendMode 混合俩种颜色
```

## 利用Quartz2D绘制动态曲线
- 三个类实现
 - UIBezierPath ,CAShapeLayer ,CABasicAnimation<br>

```objc
UIBezierPath实现绘画相应的曲线路径，CAShapeLayer 为path 提供展示的位置，并将CABasicAnimation 所创建的动画加入到Path上。
UIBezierPath *path = [UIBezierPath bezierPath]; 
[path moveToPoint:CGPointMake(0.0,20.0)]; 
[path addLineToPoint:CGPointMake(120.0, 500.0)]; 
[path addLineToPoint:CGPointMake(220, 0)]; 
[path addLineToPoint:CGPointMake(310, 40)]; 
[path addLineToPoint:CGPointMake(SCREEN_WIDTH, 110)];

然后我们再为CAShapeLayer创建自己的属性，并且将我们的path赋值给它。如果没有这个赋值的话，这个layer就不能为我们画出这个效果了，并且也是一个不完整的layer.
CAShapeLayer *pathLayer = [CAShapeLayer layer];
pathLayer.frame = self.view.bounds;
pathLayer.path = path.CGPath;
pathLayer.strokeColor = [[UIColor redColor] CGColor];
pathLayer.fillColor = nil;
pathLayer.lineWidth = 2.0f;
pathLayer.lineJoin = kCALineJoinBevel;
[self.view.layer addSublayer:pathLayer];

最后，我们将动画赋值给我们的layer.我们的动画效果中，可以改变其中的一些参数来控制它的绘画效果。
CABasicAnimation *pathAnimation = [CABasicAnimationanimationWithKeyPath:@"strokeEnd"];
pathAnimation = [CABasicAnimationanimationWithKeyPath:@"strokeEnd"];
pathAnimation.duration = 2.0;
pathAnimation.fromValue = [NSNumber numberWithFloat:0.0f];
pathAnimation.toValue = [NSNumber numberWithFloat:1.0f];
[pathLayer addAnimation:pathAnimation forKey:@"strokeEnd"];
  ```