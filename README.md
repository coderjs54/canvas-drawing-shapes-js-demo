# canvas-drawing-shapes-js-demo
## 使用纯原生js实现的在canvas中通过操作鼠标实现画简单的形状的功能

### 基本功能
    1.向画布中添加特定形状的图形,目前支持直线、描边矩形、填充矩形、描边圆形、填充圆形,并支持鼠标对图形的拖拽
    2.选择描边或者填充的颜色
    3.选择描边的线的宽度,目前支持的范围[1, 10]
    4.将当前画布以图片的形式保存到本地,目前默认导出png图片
    5.将当前画布中的图形以json文件的形式导出保存到本地,下次如果想继续在上一次的基础上添加元素时将上次的文件导入即可

### 实现画图的基本的几个关于图形的类
    1.首先定义一个基类Shape,各种具体的形状都是继承自Shape类,
        实例属性:
            color: 图形的颜色
            lineWdith: 描边宽度
            x1, y1: 图形起点的坐标,对于圆形来说表示圆心的坐标
            x2, y2: 图形终点的坐标,对于圆形来说表示圆形的弧上的点,可以根据此点坐标计算出半径的值
        实例方法:
            draw(): 在canvas上将该形状画出来
            move(): 计算拖动后的各个点的坐标
    2.Line类用来画直线,继承Shape类
        实例属性:
            shape: 用来标记该形状是直线(导入文件时需要根据该字段重新创建图形)
        实例方法:
            inside(): 用来判断画布中的一个点是否在该直线的范围内
    3.Rectangle类用来画描边矩形,继承自Shape类
        实例属性:
            shape: 用来标记该形状是描边矩形(导入文件时需要根据该字段重新创建图形)
        实例方法:
            inside(): 用来判断画布中的一个点是否在该描边矩形的范围内
    4.FillRectangle类用来画填充矩形,直接继承Rectangle类
        实例属性:
            shape: 用来标记该形状是填充矩形(导入文件时需要根据该字段重新创建图形)
        实例方法:
            inside(): 用来判断画布中的一个点是否在该填充矩形的范围内
    5.Circle类用来画描边圆形,继承自Shape类
        实例属性:
            shape: 用来标记该形状是描边圆形(导入文件时需要根据该字段重新创建图形)
        实例方法:
            inside(): 用来判断画布中的一个点是否在该描边圆形的范围内
    6.FillCircle类用来画填充圆形,直接继承Circle类
        实例属性:
            shape: 用来标记该形状是填充圆形(导入文件时需要根据该字段重新创建图形)
        实例方法:
            inside(): 用来判断画布中的一个点是否在该填充圆形的范围内
        
### 实现用户控制鼠标将图形画在画布上
    1.鼠标按下,鼠标按下后图形的起点就确定了
    2.鼠标按下后继续移动鼠标,当鼠标移动后图形的终点就确定了,在鼠标移动的过程中终点不停的在改变，因此需要不停的清空画布然后重新将最新的图形画到画布上
    3.鼠标抬起表明鼠标确定了最终的终点,此时一次画图的操作就结束了
    
### 实现用户鼠标悬浮在某个图形上后按下鼠标实现拖动行为
    1.当用户移动鼠标时,如果此时鼠标的坐标处于某个图形的范围内,那么直接调用具体图形的move()方法,清空画布然后重新将最新的图形画到画布上

### 判断鼠标是否处于图形范围内的方法
    1.对于描边的形状,直接使用浏览器内置的Path2D构造函数创建相应形状的Path2D的实例对象,然后使用CanvasRenderingContext2D:isPointInStroke方法来进行判断
    2.对于填充矩形,则需要自己手动计算
        2.1 对于填充矩形,因为矩形的四个点都可能作为起点,因此在判断的时候不能直接把起点坐标当成较小的值,终点坐标当成较大的值
        2.2 对于填充圆形,直接计算当前点与圆心之间的距离是否小于圆的半径即可判断该点是不是在圆的内部