# <font face="仿宋" color=orange>图形初阶</font>
# <center><font face="楷体" size=5>by 藏之地</font></center>
## 一、图形创建与保存
### 1.1 图形创建
    ```
    plot(x,y):泛型函数，输出将根据对象类型不同而改变
    添加type="b"同时绘制点和线。更多type参数："p"绘制散点图；"l" 绘实线;"b" 所有点被实线连接；"o" 实线通过的所有点；"h" 绘出点到x轴的竖线;"s" 绘出阶梯形曲线；"S" 同上；"n" 不绘任何点或者曲线
    pie()：饼状图
    barplot():柱状图，显示变化
    hist():直方图，统计频次，包含默认的标题，可用main=""或ann=F来禁用
    abline():在已有的图形中添加一条直线
    lines():一条直线
    boxplot():箱型图
    ```
### 1.2 图形保存
1. 将要画的图形保存：  
        ```
        pdf("file_name.pdf")#还可以使用png()、jepg()、bmp()、tiff()、xfig()、postscript()保存为其他格式
        coding#coding为所要画图形的代码
        dev.off()
        ```
2. 打开多个图形界面
    - 一般创建新图形时，会覆盖掉前面的图形，以下方法可以打开多个图形界面：
    1. 第一种  
        ```
        dev.new（）#类似于matlab中figure
        statements to create graph1
        dev.new（）
        statements to create graph2
        ```
    2. 第二种：使用历史来查看已绘制图形
    3. 第三种：使用dev.new()\dev.next()\dev.prev()\dev.set()和dev.off（）来同时打开多个图形窗口
多找绘图实例