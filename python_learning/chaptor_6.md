# 基本图形

无论在何时分析数据，第一件事就是观察它。本章将通过数据可视化来可视化单个变量分布，帮助理解单个类别型或连续型变量的图形

## 条形图

条形图通过垂直的或水平的条形展示了类别型变量的分布（频数）。函数barplot\(\)的最简单用法是：

barplot（height）

其中的height是一个向量或一个矩阵。

### 简单的条形图

例子：

library\(vcd\)\#对vcd包中的某数据集绘图

counts <\- table\(Arthritis$Improved\)\#table\(\)将各单元的计数表格化；如果本身是因子或有序型因子，可以使用plot\(\)绘制而不需要table\(\)

barplot\(counts,horiz = T\)\#水平绘制，也可以添加标签和标题

### 堆砌\(stacked\)条形图和分组\(grouped\)条形图：

如果height是一个矩阵而不是一个向量，则绘图结果将是一幅堆砌条形图或分组条形图。

参数beside=FALSE（默认值），则矩阵中的每一列都将生成图中的一个条形，各列中的值将给出堆砌的“子条”的高度。若beside=TRUE，则矩阵中的每一列都表示一个分组，各列中的值将并列而不是堆砌

例子：

counts <\- table\(Arthritis$Improved, Arthritis$Treatment\)

barplot \(counts, main= "stacked Bar Plot",

         xlab="Treatment", ylab= "Frequency",

         col=c\("red","yellow","green"\),\#三行的颜色

         legend=rownames\(counts\)\)\#堆砌条形图

barplot\(counts, main= "Grouped Bar Plot", 

          xlab="Treatment", ylab="Frequency",

          col=c\("red", "yellow", "green"\) , 

          legend=rownames\(counts\), beside=TRUE\)\#分组条形图

### 均值条形图

即在原始数据基础上先整合数据并将结果传递给barplot函数，来创建表示均值、中位数、标准差等的条形图。

例子：

states <\-data\.frame \(state\.region, state\.x77\)

means <\-aggregate \(states$Illiteracy, 

                   by=list\(state\.region\), FUN=mean\)

means <\- means\[order\(means$x\),\]

barplot\(means$x, names\.arg=means$Group\.1\)\#展示各条形标签

title\("Mean Illiteracy Rate"\)

### 条形图微调

随着条数的增多，条形的标签可能会开始重叠，你可以使用参数cex\.names=0\.8来减小字号到原来的80%使标签更合适。可选的参数names\.arg允许你指定一个字符向量作为条形的标签名。你同样可以使用图形参数辅助调整文本间隔，参见par（）

### 棘状图

棘状图是一种特殊的条形图，对堆砌条形图做了重缩放，每个条形高度为1，每一段的高度表示比例。该函数在vcd包。

## 饼图

相对于面积，人们往往对长度判断更加精确，故饼图常常不受待见\(一维图最容易把握数据\)。格式如下：

pie（x，labels=c\(“”,“”\)，col=,main=””）

其中x是一个非负数值向量，表示每个扇形的面积，而labels则是表示各扇形标签的字符型向量，可以通过paste\(\)将各扇形的值一起添label上,如下面的格式：

label<\-paste\(character\_vector,” ”,proportion比例,”%”,sep=” ”\)

3D饼图：

library \(plotrix\)

pie3D\(x, labels, explode=0\.1, main="3D Pie Chart "\)

扇形图：

作为饼图的变种，可以通过plotrix包中fan\.plot\(\)实现，

例：

library \(plotrix\)

slices <\-c\(10, 12,4, 16, 8\)

lbls <\-c\("US", "UK", "Australia", "Germany", "France"\)

fan\.plot\(slices, labels = lbls, main="Fan Plot"\)

\#各个扇形图相互叠加可见，方便比较

## 直方图

直方图通过在X轴上将值域分割为一定数量的组，在Y轴上显示相应值的频数，展示了连续型变量的分布。函数为：

hist（x）

其中的x是一个由数据值组成的数值向量。参数freq=FALSE表示根据概率密度而不是频数绘制图形。参数breaks用于控制X轴组的数量，在定义直方图中的单元时，默认将生成等距切分。

例子：

par \(mfrow=c\(2,2\)\)

hist\(mtcars$mpg\)

hist\(mtcars$mpg, breaks=12\) \#breaks指定组数

hist\(mtcars$mpg, freq=FALSE, breaks=12\)

rug \(jitter\(mtcars$mpg\)\)\#添加轴须图表现频数

\#jitter\(\)函数可以将轴须图的数据打散。如果数据中有许多“结”，即相同的值，jitter\(x,amount=0\.01\)会向每个数据点后添加一个随机值\(一个±amount之间的均匀分布随机数，以避免重复的点产生影响\)

lines\(density\(mtcars$mpg\), lwd=2\)\#添加密度曲线，作用类似轴须图

x <\-mtcars$mpg 

h<\-hist\(x, breaks=12\)\#将该直方图存成变量后，直方图的一些信息如breaks、counts、density、mids、xname、equidist等也被存到h

xfit<\-seq\(min\(x\), max\(x\), length=40\)\#length≈length\.out,先分成40份

yfit<\-dnorm\(xfit, mean=mean\(x\), sd=sd\(x\)\)\#生成正态分布

yfit <\- yfit\*diff\(h$mids\[1:2\] \)\*length\(x\)\#skip

lines\(xfit, yfit, lwd=2\)

box\(\)\#添加盒型外框

## 核密度图

核密度图能说明数据出现的次数,是一组数据在坐标轴上“疏密程度”的可视化,密度图使用拟合后的\(平滑\)的曲线显示,“峰”越高表示此处数据越“密集”,“密度”越高。绘制密度图的函数格式：

plot\(density\(x\)\)

会绘制一幅新图，叠加可用lines\(\)

polygon\(\)函数根据顶点的x和y坐标（本例中由density\(\)函数提供），连接每个相邻点\(包括始末点\)从而绘制了多边形\(足够多时成为曲型\)，对其添加颜色参数可以进行填充。

例子：

d <\- density\(mtcars$mpg\)

plot \(d, main="Kernel Density of Miles Per Gallon"\)

polygon\(d, col="red" , border="blue"\)\#填充曲线下方区域

rug\(mtcars$mpg, col="brown"\)\#添加棕色的轴须图

核密度可用于比较组间差异，需利用到sm包中的sm\.density\.compare\(\)函数，可像图形叠加两组或更多的核密度图，使用格式为：

sm\.density\.compare\(x,factor\)

其中的x是一个数值型向量，factor是一个分组变量。

例子：

library\(sm\)

attach \(mtcars\)

cyl\.f <\-factor \(cyl, levels= c\(4,6,8\), labels = c\(

  "4 cylinder", "6 cylinder","8 cylinder"\)\)

\#将数值型变量cyl转变为因子

sm\.density\.compare \(mpg, cyl,xlab="Miles Per Gallon"\)

\#比较三种cylinder的mpg

title\(main="MPG Distribution by Car Cylinders"\)

colfill <\- c \(2:\(1\+length \(levels\(cyl\.f\)\)\)\)\#作为颜色变量

legend\(locator\(1\), levels\(cyl\.f\), fill=colfill\)\#手动指定图例

detach\(mtcars\)\#但这个颜色对应还不是很清楚，不过应该可以自己添加\(skip\)

## 箱线图

箱线图（又称盒须图）通过绘制连续型变量的五数总括，即最小值、下四分位数（第25百分位数）、中位数（第50百分位数）、上四分位数（第75百分位数）以及最大值，描述了连续型变量的分布。

箱线图能够显示出可能为离群点（范围在正负1\.5\*IQR以外的值，IQR表示四分位距，即上四分位数与下四分位数的差值）的观测。

例:

boxplot\(mtcars$mpg,main="Box plot",ylab="Miles per Gallon"\)

图上whisker弦到底是指盒型各端加1\.5倍四分位距的范围，还是两最值之距，两端弦不是应该等长吗（skip）

执行boxplot\.stats\(mtcar$smpg\)即可输出用于构建图形的统计量

### 使用并列箱线图进行跨组比较

用箱线图展示分组变量，使用格式为：

boxplot（formula，data\_dataframe）

其中的formula是一个公式，一个示例公式为y~A，这将为类别型变量A的每个值并列地生成数值型变量y的箱线图。

公式y ~A\*B则将为类别型变量A和B所有水平的两两组合生成数值型变量y的箱线图;dataframe代表提供数据的数据框（或列表）。

添加参数varwidth=TRUE将使箱线图的宽度与其样本大小的平方根成正比。参数horizontal=TRUE可以反转坐标轴的方向。参数notch=TRUE可以得到含凹槽的箱线图，凹槽中心即为中位数。

例子：

boxplot \(mpg ~ cyl,data=mtcars\)\#以cyl为分类变量，以mpg为数值型变量

为多个分组因子绘制箱线图：

例子：

mtcars$cyl\.f <\-factor \(mtcars$cyl, levels=c\(4,6,8\), labels=c \("4", "6", "8"\)\)

mtcars$am\.f <\-factor \(mtcars$am, levels=c \(0,1\), labels=c \("auto", "standard"\)\)\#未转成因子型也可画

boxplot \(mpg ~ am\.f \* cyl\.f, 

         data=mtcars, varwidth=TRUE,

         col=c\("gold", "darkgreen"\)\)

\#以气缸数量cyl和变速箱类型am组合为分类变量比较每加仑燃料所行英里；从箱线图宽度也可以看出那种车型最常见

### 小提琴图

小提琴图是箱线图与核密度图的结合，使用vioplot包中的vioplot（）函数绘制它,使用格式为：

vioplot（x1，x2，\.，names=，col=）

其中x1，x2，表示要绘制的一个或多个数值向量（将为每个向量绘制一幅小提琴图）。参数names是小提琴图中标签的字符向量，而col是一个为每幅小提琴图指定颜色的向量。

例子：

library\(vioplot\)

x1 <\-mtcars$mpg \[mtcars$cyl==4\]

x2 <\- mtcars$mpg \[mtcars$cyl==6\]

x3 <\- mtcars$mpg \[mtcars$cyl==8\]

vioplot \(x1, x2, x3, names=c \("4 cyl", "6 cyl", "8 cyl"\), 

         col="gold"\)

【注意vioplot\(\)函数要求你将要绘制的不同组分离到不同的变量中，所以这里比较的时候需要分到三个变量中】

小提琴图基本上是核密度图以镜像方式在箱线图上的叠加。在图中，白点是中位数，黑色盒型的范围是下四分位点到上四分位点，细黑线表示须。外部形状即为核密度估计。

## 点图

点图提供了一种在简单水平刻度上绘制大量有标签值的方法。用dotchart（）函数创建点图，格式为：

dotchart（x，labels=）

其中的x是一个数值向量，而labels则是由每个点的标签组成的向量。你可以通过添加参数groups来选定一个因子，用以指定x中元素的分组方式。如果这样做，则参数gcolor可以控制不同组标签的颜色，cex可控制标签的大小。

例子：

x <\-mtcars\[order\(mtcars$mpg\),\]\#根据mpg进行升序排列mtcars

x$cyl <\-factor\(x$cyl\)\#以cyl为因子

x$color\[x$cyl==4\] <\-"red"

x$color\[x$cyl==6\] <\-"blue"

x$color\[ x$cyl==8\]<\-"darkgreen"\#做颜色变量

dotchart\(x$mpg,labels = row\.names \(x\),cex=\.7, 

        groups =x$cyl,gcolor = "black", \#将该因子设为x中分组，黑色

        color = x$color, \#点和标签的颜色

        pch=19\)

这样，就可以观察不同cyl的每种车型的mpg情况；但数据点增多，点图实用性降低（Hmisc包提供许多点图附加功能）
