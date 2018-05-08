## 事件处理简介
- 事件处理简介
    - 在用户使用app过程中，会产生各种各样的事件
    - iOS中的事件可以分为3大类型:`主要了解触摸事件`<br>
    ![](/assets/eventType.png)
    - 什么是响应者对象?
        - 在iOS中不是任何对象都能处理事件，只有继承了UIResponder的对象才能接收并处理事件。我们称之为`响应者对象`
        - UIApplication、UIViewController、UIView都继承自UIResponder，因此它们都是响应者对象，都能够接收并处理事件
    - 为什么继承UIResponder就能处理事件
        - UIResponder内部提供了以下方法来处理事件<br>
        ```objc
            触摸事件
            - (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event;
            - (void)touchesMoved:(NSSet *)touches withEvent:(UIEvent *)event;
            - (void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event;
            - (void)touchesCancelled:(NSSet *)touches withEvent:(UIEvent *)event;

            加速计事件
            - (void)motionBegan:(UIEventSubtype)motion withEvent:(UIEvent *)event;
            - (void)motionEnded:(UIEventSubtype)motion withEvent:(UIEvent *)event;
            - (void)motionCancelled:(UIEventSubtype)motion withEvent:(UIEvent *)event;

            远程控制事件
            - (void)remoteControlReceivedWithEvent:(UIEvent *)event;
        ```
    - 想处理触摸事件，应该怎么办? - 直接看实例

## UIView拖拽演练
- 为什么要自定义view:系统自带不能处理事件
- 演示触摸事件方法,触摸的完整过程<br>

```objc
UIView是UIResponder的子类，可以覆盖下列4个方法处理不同的触摸事件
一根或者多根手指开始触摸view，系统会自动调用view的下面方法
- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event

一根或者多根手指在view上移动，系统会自动调用view的下面方法（随着手指的移动，会持续调用该方法）
- (void)touchesMoved:(NSSet *)touches withEvent: (UIEvent *)event

一根或者多根手指离开view，系统会自动调用view的下面方法
- (void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event

触摸结束前，某个系统事件(例如电话呼入)会打断触摸过程，系统会自动调用view的下面方法
- (void)touchesCancelled:(NSSet *)touches withEvent:(UIEvent *)event
提示:touches中存放的都是UITouch对象
```

- 介绍参数(NSSet,UITouch,UIEvent)
    - `重点UITouch`
        - 触摸事件方法中的UITouch都是同一个对象，因为一根手指对应一个UITouch.当手指移动或者抬起，并不会产生一个新的UITouch对象给你，而是改变UITouch里面的属性
        - 默认三个方法里面只能获取到一个手指，为什么。UIView不支持多点触控
        - 怎么才能有两个手指，两个手指同时按，并且视图支持多点触控
        - UITouch的tapCount有什么用？可以判断用户当前是双击还是单击
        - UITouch的phase有什么用? 根据这个属性，判断当前需要调用哪个处理事件方法，begin,move,end
        
```objc
程序思路：
* 在TouchMove里面做事情-为什么?因为用户手指在视图上移动的时候才需要移动视图。
* 获取用户当前的位置，获取用户之前的位置，就知道用户从哪    移动到哪,这个位置也是视图移动的位置
* 当前视图的位置 = 上一次视图的位置 + 手指的偏移量
```

## 事件传递(学事件传递，谁有权利处理事件)
    - 事件，加入到一个由谁管理的事件队列中?UIApplication
    - 为什么用队列，不用栈。队列先进先出，意味着先产生的事件，先处理。
- 代码验证事件谁处理
    - PPT上这么多view，验证哪个view处理事件。这么多view，都需要监重写一个方法，搞个父类。
    - 一个view能处理事件，意味着事件传递给他了，那怎么传递? 事件是由父控件传递给子控件。
    - 父控件不处理事件，子控件也不能。蓝色不接收事件，黄色也不会接收事件? 为什么，因为事件是从父控件传递给子控件的。父控件都没有事件，怎么传给子控件。
- 代码验证view不能处理事件
    - 一个view怎么不能处理事件?
    
    ```objc
    userInteractionEnabled = NO
    hidden = YES
    alpha <= 0.01
    ```

- 代码验证UIImageView不允许交互
    - UIImageView默认不允许用户交互，因此默认它上面的子控件不能接收事件。
- 怎么找到最合适的view？通过一个递归。
    - 第一个接收事件的控件是谁?窗口
    - 当事件传递给窗口的时候，就会让窗口去找最合适的view
    
    ```objc
    1> 判断自己能不能接收事件 
    2> 点在不在窗口上 
    3> 去找比自己更合适的view，从后往前遍历子控件，拿到子控件后，把事件传递给这个子控件 
    4> 子控件拿到事件之后，又会做同样的判断，一直递归去找，直到找到最合适的view.
    ```

- 事件传递的目的何在?找到最合适的view,把事件交给他。

## hitText方法和pointInside方法
- （了解hitText）学习一个方法必须了解：什么时候调用和这个方法有什么用
    - hitText什么时候调用:当一个事件传递给一个控件的时候，控件就会调用这个方法
    - hitText作用: 寻找到最合适的view。
    - (回顾下事件传递)UIApplication -> UIWindow
    - UIWindow去寻找最合适的view? [UIWindow hitTest:withEvent:]里面做了什么事情？
        - 判断窗口能不能处理事件? 如果不能，意味着窗口不是最合适的view，而且也不会去寻找比自己更合适的view,直接返回nil,通知UIApplication，没有最合适的view。
        - 判断点在不在窗口
        - 遍历自己的子控件，寻找有没有比自己更合适的view
        - 如果子控件不接收事件，意味着子控件没有找到最合适的view,然后返回nil,告诉窗口没有找到更合适的view,窗口就知道没有比自己更合适的view,就自己处理事件。
    - 验证下hitTest方法返回nil，里面的子控件能处理事件吗？ 重写根控制器view的hitTest:withEvent:方法，
    - 验证这个方法是否真能找到最合适的view？
    - 如果点击屏幕任何一个地方，都是白色的view，怎么做。直接返回白色的view,就不会继续去找白色view的子控件了。
- 介绍pointInside方法
    - pointInside作用：判断一个点在不在一个控件上
    - point参数:方法调用者坐标系上的点，PPT画图分析原理。
    - `重点`:学习完了pointInside,就能实现下hitTest方法底层是怎么做的了。
    
```objc
1.自定义窗口(继承UIWindow重写就行)
2.hitTest:withEvent: 作用:就是用来寻找最合适的view(这个方法只有可能控件才有,
事件就是再控件之间相互传递)
/**
 * 事件传递的时候调用
 * 什么时候调用:当事件传递给控件的时候调用这个方法
 * 作用:寻找最合适的view
 * point : 当前的触摸点 这个点的坐标系就是方法调用着的坐标系
 */
- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event
{
    // 调用系统的方法去寻找最合适的view:肯定返回最合适view
    UIView *fitView = [super hitTest:point withEvent:event];

    NSLog(@"%s----%@",__func__,fitView);

    // 那么我们就可以在这里拦截让谁来处理
    return fitView;
}
```

## hitTest的底层实现
```objc
/**
 * 自己实现底层的实现
 */
- (UIView *)hitTest:(CGPoint)point withEvent:(UIEvent *)event
{
    // 1.判断当前控件能否接收事件
    if (self.userInteractionEnabled == NO || self.hidden == YES || self.alpha <= 0.01f) return nil;

    // 2.判断点在不在这个控件身上
    if ([self pointInside:point withEvent:event] == NO) return nil;

    // 3.从后往前遍历自己的子控件
    NSInteger count = self.subviews.count;

    for (NSInteger i = count - 1; i >= 0; i--) {

        UIView *childView = self.subviews[i];

        // 把当前控件的坐标系转换成子控件的坐标系
        CGPoint childP = [self convertPoint:point toView:childView];

        UIView *fitView = [childView hitTest:childP withEvent:event];

        if (fitView) { // 寻找到最合适的view
            return fitView;
        }
    }
    return self;
}
```

## hitTest的2个小练习
```objc
1.一个按钮在一个view(alpha == 0.4)下面,要求点击view如果触摸点在按钮上的时候按钮响应事件，如果在view其余地方则view相应事件?
    - 分析事件传递: 当黄色要处理事件，首先事件得传递到他身上
    - 重写hitTest方法：事件传递到某个控件，调用什么方法?    hitTest
    - 返回nil什么意思？如果直接返回nil，意味着黄色的view，没有找到最合适的view,他的父控件，就会遍历下一个控件，也就是按钮，询问按钮是不是最合适的view.
    - 判断点在不在按钮上，在就交给他处理。
    - pointInside实现。
2.超出父控件的边界的子控件能够显示出来，如何做到超出父控件的子控件也能响应事件，将父控件坐标系转到子控件坐标系时判断点如果在则响应事件，如果不在则走系统正常模式?
```

## 触摸事件处理的详细过程
```objc
1.用户点击屏幕后产生的一个触摸事件，经过一系列的传递过程后，会找到最合适的视图来处理这个事件。
2.找到最合适的视图控件后，就会调用控件的touches方法来作具体的事件处理
>touchesBegan...
>touchesMoved...
>touchesEnded...
3.这些touches方法的默认做法是将事件顺着响应者链条向上传递，将事件交给上一个响应者进行处理
4.如果调用了[super touches...];将会将事件顺着响应者链条往上传递，传递给上一个响应者
5.接着就会调用上一个响应者的touches...方法

如何判断上一个响应者
1.如果当前这个view是控制器的view,那么控制器就是上一个响应者
2.如果当前这个view不是控制器的view,那么父控件就是上一个响应者
```

## 响应者链条
- 响应者链条：是由多个响应者对象连接起来的链条
- 作用：能很清楚的看见每个响应者之间的联系，并且可以让一个事件多个对象处理。
- 响应者对象：能处理事件的对象
- 学了响应者链条，目的知道谁最终处理事件。
    - touch默认做法:自己不处理事件，交给上一个响应者处理touch事件。
    - 响应者链条，点击绿色的view,如果不处理事件，就会往上传递。
    - 验证touch的默认做法 先恢复所有view的默认做法
    - 监听黄色点击，蓝色点击。
    - 黄色调用默认做法，事件传递给谁处理?蓝色
    - 得出结论：
        - 1> touch的默认做法：自己不处理，交给上一个响应者。 
        - 2> 上一个响应者默认是父控件
    - 两个view怎么同时处理事件?一个view处理方法，在调用父类默认的做法
    - 把事件传递给白色的view，怎么做?
    - 总结下事件传递的完整过程.
    - 把事件传递给控制器，测试白色view的上一个响应者是否是控制器。
    - 响应者链条示意图<br>
    ![](/assets/WX20180206-141244.png)

![](/assets/assets:CAAnimation类继承结构图.png)