# 图形初阶

## 图形创建与保存

### 图形创建

plot\(x,y\):泛型函数，输出将根据对象类型不同而改变

添加type=“b”同时绘制点和线。更多type参数：p" 绘[散点图](https://so.csdn.net/so/search?q=%E6%95%A3%E7%82%B9%E5%9B%BE" \t "https://blog.csdn.net/weixin_33762130/article/details/_blank)；"l" 绘实线  
"b" 所有点被实线连接；"o" 实线通过的所有点；"h" 绘出点到x轴的竖线

"s" 绘出阶梯形曲线；"S" 同上；"n" 不绘任何点或者曲线

pie\(\)：饼状图

barplot\(\):柱状图，显示变化

hist\(\):直方图，统计频次，包含默认的标题，可用main=””或ann=F来禁用

abline\(\):在已有的图形中添加一条直线

lines\(\):一条直线

boxplot\(\):箱型图

### 图形保存

将要画的图形保存：  
pdf\(“file\_name\.pdf”\)\#还可以使用win\.metafile\(\)、png\(\)、jepg\(\)、bmp\(\)、tiff\(\)、xfig\(\)、postscript\(\)保存为其他格式

   coding\#coding为所要画图形的代码

dev\.off\(\)

一般创建新图形时，会覆盖掉前面的图形，以下方法可以打开多个图形界面：

第一种  
dev\.new（）\#类似于matlab中figure

  statements to create graph1

dev\.new（）

  statements to create graph2

第二种

使用历史来查看已绘制图形

第三种

或者使用dev\.new\(\)\\dev\.next\(\)\\dev\.prev\(\)\\dev\.set\(\)和dev\.off（）来同时打开多个图形窗口

## 图形原有参数

包括符号和线条、颜色、文本属性？、图形尺寸和边界大小这些图形非附加参数

1使用par函数

par\(option\_name=value,\.\.\., no\.readonly= FALSE\):value为该参数要修改成的值

no\.readonly=TRUE时，par（）将只允许读这一个参数，会返回一个可以修改的当前绘图设备中各参数的参数值列表，不加参数执行par（）将生成一个含有当前图形参数列表

例子：

opar <\- par\(no\.readonly = TRUE\)\#全局修改，记录原始的默认样式

par\(lty=2,pch=17\)\#也可以分开单独写成par\(lty=1\) par\(pch=17\)

par\(opar\)\#还原原始设置

  
2直接在绘图函数中添加option=value

plot\(x,y,type="b",lty=2,pch=17\)

【指定的选项只对这幅图本身有效，但并不是所有绘图函数都允许指定全部可能的图形参数】

图形参数设置的列表，更多参数查看请点击：[图形参数](https://blog.csdn.net/glodon_mr_chen/article/details/79293615)

### 指定符号和线条类型的options：

cex\(character expansion\)  lty\(line type\)  lwd\(line width\)

对于符号21\-25还可以指定边界颜色（col=）和填充色（bg=）

### 用于指定颜色的options

包括图颜色、坐标轴刻度颜色、坐标轴标签颜色、标题颜色等

本节包括一般颜色生成、创建连续型颜色的函数、RColorBrewer包的使用以及灰度

简单颜色生成：

在R中，可以通过指定颜色下标、颜色名称、十六进制的颜色值、RGB值或HSV值来指定颜色。例如，col=1,col=”white”,col=”\#FFFFFF”、col=rgb\(1,1,1\)和col=hsv\(0,0,1\)均可以表示白色（值范围在0\-1）。rgb\(\)可基于红蓝绿三色值生成颜色，hsv\(\)基于色相\-饱和度\-亮度值来生成颜色。

colors\(\)：返回所有可用颜色的名称

可以以col=c\(\)创建颜色向量

创建连续型颜色向量的函数，如：

rainbow\(\)、terrain\.colors\(\)、heat\.colors\(\)、topo\.colors\(\)、cm\.colors\(\)等，例如rainbow\(10\)表示生成10种连续的彩虹颜色，但好像只改变了绘图符号的颜色，改变分段线条的颜色会用到ggplot2包

利用RColorBrewer包：

例子：

n <\- 7\#首先创建一个颜色值的向量

mycolors <\- brewer\.pal\(n,"Set1"\)\#从Set1调色板中抽取7种用十六进制表示的颜色并返回一个向量；键入brewer\.pal\.info可看到可选调色板的列表信息，键入；display\.brewer\.all\(\)可以直观看到可选调色板的图形

barplot\(rep\(1,n\),col=mycolors\)

     

多阶灰度色：

利用R自带的gray\(\),通过元素值为0和1之间的向量值来指定各颜色的灰度，如gray\(0:10/10\)将生成10阶灰度色

例：

n<\-10

mygrays <\- gray\(0:n/n\)\#生成7阶灰度

pie\(rep\(1,n\),labels = mygrays,col = mygrays\)\#labels标签

### 文本属性

包括坐标轴、标题文字大小、字体样式

用于指定文本大小的options：

用于指定字体、字号与字样的options：

对于family="serif"这样的映射不满意的可以通过windowsFont\(\)来创建这类映射。

如果是以PDF格式输出图形，可以采用names\(pdfFonts\(\)\)找出系统中有哪些字体是可用的，然后使用pdf\(file=”file\_name\.pdf”,family=”fontname”\)来生成图形；对PostScript格式，对应的pdf换成PostScript即可

### 控制图形尺寸和边界大小的参数

par\(pin=c\(4,3\),mai=c\(1,\.5,1,\.2\)\#生成一幅4英寸宽、3英寸高，上下左右分别我1、1、0\.5、0\.2英寸的图形\(后者才是图形实际大小\)

图形参数案例：

opar <\- par\(no\.readonly = T\)

par\(pin=c\(2,3\)\)\#上下宽2、3英寸

par\(lwd=2,cex=1\.5\)\#线宽为默认的2倍，符号大小为默认1\.5倍

par\(cex\.axis=\.75,font\.axis=3\)\#坐标轴刻度文字缩放为默认的75%和文字样式为斜体

plot\(dose,drugA,type="b",col=“green”,lty=2,pch=17\)

plot\(dose,drugA,type="b",col=“red”,lty=2,pch=17,bg="green"\)\#多加了符号填充色

par\(opar\)\#还原原始设置，画的图一定要这两句在里面，否则后面的不再追加到已有图形

\#通过par（）设置的参数对两幅图都有效，plot\(\)中只对内部图形有效

## 添加文本、自定义坐标轴和图例

许多高级绘图函数如plot、hist、boxplot才允许自行设定，某些高级绘图函数已经包含了默认的标题和标签,可以在plot\(\)语句或单独par\(\)语句中添加ann=FALSE来移除他们

某些绘图函数自行设定、用于添加图形标题、坐标轴的options：

main=””:添加标题

sub=””:添加副标题

xlab=””或ylab=””：添加坐标轴标签

xlim=c\(\)或ylim=c\(\):指定坐标轴范围

再次提醒：并非所有的绘图函数都具有这些options，可借助帮助函数查看。

本节所示以下函数可以更精细和模块化地控制标题、坐标轴、图例和标注；可在现有图形基础上添加相应图形参数

### 标题

title\(main=””,sub=””,xlab=””,ylab=””\):添加标题和坐标轴标签

title\(main=””,col\.main=“”,sub=””,col\.sub=\.\.,xlab=””,ylab=””,col\.lab=“”,cex\.lab=value,line=2\):生成不同颜色的标题以及副标题，特定大小的坐标轴标签,line参数表示标签离坐标轴距离

title\(\)一般被用于添加信息到一个默认标题和坐标轴标签被ann=FALSE选项移除的图形中,否则会出现重叠的标签

### 坐标轴

同样地，使用axis\(\)自定义坐标轴时，应当用axes=FALSE禁用高级绘图函数自动生成的全部坐标轴（包括坐标轴框架线，除非你添加参数frame\.plot=TRUE）,参数xaxt=”n”和yaxt=”n”将分别禁用X轴和Y轴（会留下框架线，只是去除了刻度）

坐标轴options：

图形太小刻度标签可能会不显示；las\(lying as\)值一般使用2

案例：

x <\- c\(1:10\)

y <\- x

z <\- 10/x

\#生成数据

opar <\- par\(no\.readonly = T\)

par\(pin=c\(3,4\)\)\#图形的边界大小

plot\(x,y,type="b",col=mycolors,\#画x对y的点线图，颜色为预设的mycolors

     yaxt="n",lty=1,\#禁用Y轴刻度，线条为直线

     ann=FALSE,pch=17\)\#移除默认标题标签

lines\(x,z,type="b",pch=20,\#对现有图形添加点线图，符号为20号黑点

      col="red",lty=2\)\#颜色为红，2号虚线

title\("an example of 10/x",\#只有一个标题时可省去main=

      col\.main="blue",line=2,\#默认为黑色

      xlab="x",ylab="y=x",\#两坐标轴标签

      col\.lab="red",cex\.lab=2\)\#标签颜色和大小

axis\(2,at=x,labels=x,\#左边绘制坐标轴，刻度与x值位置均一致

     col\.axis="purple",las=2\)\#标签垂直坐标轴，紫色

axis\(4,at=z,labels=round\(z,digits=2\),

\#图形右边坐标轴，位置在z上，数值为四舍五入且保留两位有效小数

     col\.axis="purple",\#颜色为紫色

     las=0,cex\.axis=0\.7,tck=\-\.01\)

\#坐标平行坐标轴，刻度大小，坐标轴外面\(负值\)的刻度线长度变小    

mtext\("y=1/x",side=4,line=1,\#右边添加文本，离坐标轴距离为1

      cex\.lab=1,las=2,col="brown"\)\#文本标签大小，垂直与坐标轴，棕色

par\(opar\)\#还原原始设置

\#其中lines是在现有图形上添加新的图形元素，mtext是在图形边界添加文本

添加次要刻度线：

需要使用到Hmisc包中的minor\.tick\(\)函数

minor\.tick\(nx=n,ny=n,tick\.ratio=n\)\#nx和ny指定X、Y轴两条主刻度线之间通过次要刻度线划分得到的区间个数，tick\.ratio表示次要刻度线相对于主刻度线的大小比例

minor\.tick\(nx=3,ny=6,tick\.ratio = 0\.5\)\#在X轴添加每两条刻度线之间分为三段，并将Y轴的相同长度刻度的每两条主刻度线之间分成6段，即后者添加4条次要刻度线

### 参考线

abline\(h=yvalues,v=xvalues\)\#h表示在y轴画水平线，v表示在x轴画竖直线

abline\(h=c\(1,5,7\),v=seq\(1,10,2\),lty=2,col="pink"\):在y=1,5,7添加水平实线，在x=奇数时添加竖直线，并设置线样式颜色

### 图例

图例帮助辨别所画图形中的多组数据的条形。使用格式：

legend \(location, title, legend, \.\.\.\)

其他常用的图例选项包括用于指定盒子样式的bty、指定背景色的bg、指定大小的cex，以及指定文本颜色的text\.col。指定horiz=TRUE将会水平放置图例，而不是垂直放置。

legend\("topleft",inset=\.05,\#指定位置右上

       title="Drug Type", c\("A","B","C"\),\#图例标题及

       lty=c \(1, 2\) , pch=c \(17, 20\),\#前两条的样式和符号

       col=c \("black", "red"\)\)

### 文本标注

通过函数text（）和mtext（）将文本添加到图形上。text（）可向绘图区域内部添加文本，而mtext（）则向图形的四个边界之一添加文本。

text \(location, "text to place", pos, \.\.\.\) 

mtext \("text to place", side, line=n, \.\.\.\)\#line参数应该是表示标签离坐标轴距离

其他常用的选项有cex，col和font（分别用来调整字号、颜色和字体样式）

text\(\)用来标示点：

例子：

attach\(mtcars\)

plot\(wt,mpg,main="title",

     xlab="left",ylab="right",

     pch=18,col="blue"\)

text\(wt,mpg,row\.names\(mtcars\),\#以wt和mpg的点作图，以标签名作为文本

     cex=\.9,pos=4,col="red"\)

detach\(mtcars\)

### 添加数学标注

函数plotmath（）可以为图形主体或边界上的标题、坐标轴名称或文本标注添加数学符号。

demo\(plotmath\)\#获得该函数的部分运行结果，demo表示演示。

算术运算符\(Arithmetic Operators\)  根\(Radicals） 

下标/上标\(Sub/Superscripts\)  关系\(Relation\)  并列\(Juxtaposition\)              

字型\(Typeface\)  列表\(Lists\)

更多使用：help\(plotmath\)

## 图形的组合

在R中使用函数par\(\)或layout\(\)可以容易地组合多幅图形为一幅总括图形。

通过par\(\)函数参数

使用图形参数mfrow=c（nrows，ncols）来创建按行填充的、行数为nrows、列数为ncols的图形矩阵。另外，可以使用nfcol=c（nrows，ncols）按列填充矩阵。

例：

par\(mfrow=c\(2,2\)\)\#按列填充几个图形，2行2列

layout\(\)函数

layout\(matrix\(c\(1,1,2,3\),2,2,byrow=T\)\)\#c\(1,1,2,3\)表示三个图中第一个图占据第一行的1,2两列，而第二、第三个图分别占据第二行的1,2列，可为0

layout\(matrix\(c\(1,4,2,3\),2,2,byrow=T\),widths=c\(1,1\),heights = c\(1,5\)\)\#四个图，各列\(第一二\)宽度为1:1，各行高度比为1:5

图形布局的精细控制：

利用参数fig来布局或叠加创建单幅、有意义的图形

例如创建散点图加上两边的两幅箱线图：

attach\(mtcars\)

opar <\- par\(no\.readonly = T\)

par\(fig=c\(0,0\.8,0,\.8\)\)\#为c\(x1,x2,y1,y2\)的数值向量

plot\(wt,mpg,xlab="bbb",ylab="nnn"\)

par\(fig=c\(0,\.8,\.5,1\),new=T\)

boxplot\(wt,horizontal=T,axes=F\)\#水平放置，去掉外边轴框

par\(fig=c\(\.6,1,0,\.8\),new=T\)

boxplot\(mpg,axes=F\)

mtext\("vvv",side=3,outer=T,line=\-3\)\#3表示在上面，至于整幅图的外边缘，标签的位置

par\(opar\)

detach\(mtcars\)  
\#具体的位置需要根据实际调节设置