## iOS开发之Core Animation(核心动画）

# Core Animation简介
- Core Animation直接作用在CALayer上,非UIVIew;
- Core Animation,中文翻译为核心动画,它是一组非常强大的动画处理API，使用它能做出非常炫丽的动画效果,而且往往是事半功倍。也就是说，使用少量的代码就可以实现非常强大的功能。
- Core Animation是跨平台的,可以在mac和iOS中通用。
- Core Animation动画执行时是后台操作,所以不会堵塞主线程。（不阻塞主线程,即苹果为我们在执行完动画block后主动为我们释放了block对象,亦可以理解为在执行动画的时候还能执行操作）。

# Core Animation的使用步骤
- 1.使用它需要先添加QuartzCore.framework框架和引入主头文件<QuartzCore/QuartzCore.h>(iOS7以后不需要)
- 2.初始化一个CAAnimation对象，并设置一些动画相关属性
- 3.通过调用CALayer的addAnimation:forKey:方法增加CAAnimation对象到CALayer中，这样就能开始执行动画了
- 4.通过调用CALayer的removeAnimationForKey:方法可以停止CALayer中的动画

# CAAnimation的使用(`核心`)
- CAAnimation类继承结构图
![](/assets/CAAnimation类继承结构图.png)
- CAAnimation是所有动画类的父类,但是它不能直接使用,应该使用它的子类。(`注意`)
- CAAnimation常见属性<br>
```objc
duration：动画的持续时间
repeatCount：动画的重复次数
timingFunction：控制动画运行的节奏
```
- CAAnimation能用的动画子类<br>
```objc
CABasicAnimation
CAKeyframeAnimation
CATransition
CAAnimationGroup
```

# CAPropertyAnimation的使用
- CAPropertyAnimation是CAAnimation的子类,但是不能直接使用,要想创建动画对象,应该使用它的两个子类：
  - CABasicAnimation和CAKeyframeAnimation<br>
  ```objc
  它有个NSString类型的keyPath属性,你可以指定CALayer的某个属性名为keyPath,并且对CALayer的这个属性的值进行修改,达到相应的动画效果。比如,指定@"position"为keyPath，就会修改CALayer的position属性的值，以达到平移的动画效果
  ```
  
# CAMediaTiming动画协议(protocol)属性解析
```objc
duration：动画的持续时间
repeatCount：动画的重复次数
repeatDuration：动画的重复时间
removedOnCompletion：默认为YES,代表动画执行完毕后就从图层上移除,图形会恢复到动画执行前的状态。如果想让图层保持显示动画执行后的状态,那就设置为NO,不过还要设置fillMode为kCAFillModeForwards
fillMode：决定当前对象在非active时间段的行为。比如动画开始之前,动画结束之后
beginTime：可以用来设置动画延迟执行时间,若想延迟2s,就设置为CACurrentMediaTime()+2，CACurrentMediaTime()为图层的当前时间
timingFunction：速度控制函数，控制动画运行的节奏
delegate：动画代理
```
  
# 补充说明
- 所有动画对象的父类,负责控制动画的持续时间和速度,是个抽象类,不能直接使用,应该使用它具体的子类





