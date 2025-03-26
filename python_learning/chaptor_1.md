# 杂七杂八

## 函数集合

### R功能函数（python包、help、R工作空间等）

包含对python包操作、帮助函数、软件设置方面的操作

#### python包函数

包外操作（不涉及包的使用）：

R\.version：查看当前R的版本

library\(\):查看当前库里的包

\.libPaths\(\)：查看库（library）位置（包括用户的个人库和R的系统库，后者）

	第一个路径"C:/Users/mingweiwang/AppData/Local/R/win\-library/4\.3"是当前用户的个人库文件夹路径，其中包括用户安装的R包。

第二个路径"C:/Program Files/R/R\-4\.3\.2/library"是R的系统库文件夹路径，其中包括系统安装的R包。

pip install numpy \#下载vcd包，默认是最新的

install\.packages\(c\("vcd",”caer”\)\#下载两个或多个包

import name:加载名为name的包；

installed\.packages\(\)或installed\.packages\(“name”\):列出已安装的包的相关信息

packageVersion\("package\_name"\)：查看某个包的版本信息（比上面那个有用感觉）

search\(\):查看当前环境中哪些包已被加载

detach\("package:vcd"\)：从当前环境下移除已加载的包

update\.packages\(\):更新安装的包

remove\.package\(“vcd”\):删除已下载vcd包

包内操作：

help\(package="vcd"\)：查看vcd包的帮助文档\(包括包的函数和数据集\)，或使用library\(help="vcd"\)

ls\("package:vcd"\)：查看已加载包所含函数

data\(package="vcd"\)：查看已加载包的数据集

#### 帮助函数

针对packages：

help\.start\(\):打开帮助文档首页，进入浏览器

help\(package=ggplot2\)：查看包的帮助文档，””可加可不加

vignette\(“”\):可以查看更多的关于包的内容（即显示vignette文档，vignette意为简介）

vignette\(\):显示已安装包中所有可用的vignette文档

针对function：

help\(\)或者?接函数名:查看函数帮助文档\(常用\)

??接函数名:当未加载包时直接看函数的帮助文档

args\(\):查看函数所用的参数而不需打开帮助文档

example\(\):查看函数的实例，绘图函数还会给出图

更多：

help\.search\(“”\):在本地进行模糊搜索一个函数，比如搜索绘制热图heatmap的函数；也可以直接使用??heatmap进行本地搜索

RSiteSearch\(""\)：通过网络进行搜索某关键字\(包括在线文档和R\-Help邮件列表的讨论文章\)

apropos\("sum"\):列出所有包含sum字段的内容

apropos\("sum",mod="function"\)：搜索含sum字段且是函数的内容（或用mode）

#### R工作空间、history函数

包括R当前工作环境、环境中变量、参数等的操作

工作目录：

getwd\(\):显示当前的工作目录

setwd\("mydirectory"\):修改当前的工作目录为mydirectory，\(注意是“/”\)但不会直接创建新的；如果想用相对目录，可以像Linux一样使用\./new\_directory来表示当前目录下的某个目录；

dir\.create\(\):创建新目录

list\.files\(\):显示当前工作目录的文件

file\.remove\('myfile'\)：删除当前目录下的myfile文件

工作空间中的对象：

ls\(\):列出当前工作空间\(环境\)中的对象或变量

ls\.str\(\):显示环境中变量及其信息

rm\(obj\):移除（删除）一个或多个对象

rm\(list=ls\(\)\)：清空当前环境中所有的变量，此处的list是一个option参数，这是非常常用的一个操作；

环境中参数操作：

help\(options\):显示可用选项的说明

options\(\):显示或设置当前选项,包括数字位数、下载扩展包的联接网址、光标设置、错误信息提示、设置R控制台、R语言计算相关的属性等，如options\(digits=3\)表示将数字显示为具有三位有效数字的格式\.更多请看[options（）](https://www.jianshu.com/p/25595c80d8bb)

更多：

history\(\#\):显示最近使用过的命令（默认值为显示25个）

savehistory（"myfile"）:保存命令历史到文件myfile中（默认值为\.Rhistory）

loadhistory（"myfile"）：载入一个命令历史文件（默认值为\.Rhistory）

save\.image（"myfile\.RData"）：保存工作空间的环境中的变量和函数到文件myfile中（默认值为\.RData），但不会保存图片；

save \(obj，file="myfile\.RData"\):保存指定对象到一个文件中

load（"myfile\.RData"）:读取一个工作空间\(\.RData文件\)到当前会话中（默认值为\.RData）

q\(\):退出R，将会询问你是否保存工作空间

#### R文件输入

source\(“filename”\):执行包含在该工作目录下指定文件中的R语句集合

#### 结果输出与保存

sink\(“filename”\):将以下代码的输出结果保存到指定文件中，默认情况下，若文件已经存在，则它的内容将被覆盖；参数append=TRUE\(可简写为T\)可以将文本追加到文件后而不是覆盖；参数split=TRUE可以将输出同时发送到屏幕和输出文件中；

sink\(\)：仅输出到控制台

以下是一些图片的保存skip

bmp（"filename\.bmp"）:图片以BMP格式输出到指定文件

jpeg（"filename\.jpg"\):JPEG文件

pdf（"filename\.pdf"）:PDF文件

png（"filename\.png"）:PNG文件

postscript（"filename\.ps"）:PostScript文件

svg（"filename\.svg"）:SVG文件

win\.metafile（"filename\.wmf"）:Windows图元文件

dev\.off\(\):将输出返回到终端（保存图片之后使用）

dev\.new\(\)

### 数据类型相关函数

head\(\):访问数据的前几行，默认为5吧

tail\(\)

str\(object\):查看对象信息

#### 向量

length（x）:查看一个向量的长度

创建：

rep\(x,times=n,each=m\):先指定需要重复生成的值或向量，再设定x整体要重复的次数和循环的次数。即x中每个数字先重复m次再循环n次

runif（n,min=a,max=b）:得到均匀分布的随机样本，即生成在a\-b之间的n个随机数，runif意为random samples from uniform distribution

seq\(a,b,length\.out=n\):将a到b分成n份，R中没有1:2:5这种输入方式

append\(x=x,value=n,after=m\):在向量a中第m个数值后追加一个值为n的数值

intersect\(x,y\)：对x和y两个向量取子集；

运算：

x\*\*n\(或y\),乘幂运算，等于x^n

x%%n\(或y\)取余运算，x%/%n\(或y\)整除运算

abs\(\):计算绝对值

sqrt\(\):计算平方根

log\(x,base=a\):求以a为底的的对数，无base默认为自然对数；log10\(x\)可直接用

exp\(\):计算指数

ceiling\(x\):返回不小于x的最小整数

floor\(x\):返回不大于x的最小整数

trunc\(x\):返回x的整数部分

round\(x,digits=n\):对x四舍五入,保留位数为n位小数

signif\(x,digits=n\):四舍五入，保留n位有效数字

sum\(x\):求和

max\(x\)：最大值，min最小值

range（x）：返回最大值和最小值

mean（x）：均值

var（x）：方差

sd（x）：标准差

prod（x）：连乘的积

median（x）：中位数

quantile（x，probs=c\(0\.3,0\.5,0\.9\)）：计算30%、50%、90%百分位数，默认四分位

which\(\)：返回值所在位置，如which\.max\(x\):返回最大值的位置索引值，也可以用min；which（x==n）返回x中值为n的位置；which\(x>n\)返回大于n的位置

#### 矩阵

matrix\(x,nrow=numbers\_of\_rows,ncol=n\_o\_c,byrow=T,dimnames=list\(char\_vector\_rownames,char\_vector\_colnames\)\):其中x包含矩阵元素；nrow和ncol指定行和列数；byrow=T表示按行填充，默认按列；char\_vector\_colnames为字符型向量，其中的也可以在后面补加:

dimnames（matrix\_name） <\- list\(c\("b","c"\),c\("s","d","c"\)\)列名行名

矩阵下标：

x\[n,\]表示返回矩阵x中第n行全部数值

x\[n,c\(\)\]:返回矩阵x中第n行的c（）数值

x\[\-1,n\]:返回第n列中除第一个数值

也可以通过名称索引

dim\(x\)<\-c\(m,n\):添加维度变成矩阵

矩阵运算：

colSums\(x\):计算矩阵x每一列的和,有NA则会计算成NA

rowSums\(x\):

colMeans\(x\):

x\*x：对应元素相乘，称为内积

x %\*% x:计算外积，

diag\(x\):返回对角线数值

t（x）：进行转置

#### 列表

dim\(x\)<\-c\(m,n,o\):给x添加维度变成数组

array\(x,numeric\_vector\_dimension,char\_list\_dimnames\)\#x包含数组中数据；dimension给出各维度最大值；

#### 数据框

data\.frame\(col1,col2,col3,\.\.\.,stringsAsFactors = FALSE\):列向量可以使任何类型，每一列的名称可由函数name指定。

数据框索引：

[提取数据框元素](https://www.jianshu.com/p/365bf1bd4481)

同样可以用\[\] or \[c\(\)\]

data\_frame\_name\["name"\]:返回数据框中的name这一列，为一个数据框

data\.frame\_name\[,"name"\]:返回数据框中name这一列，为一个vector

data\.frame\_name$name:类似前者，常用

attach\(data\.frame\_name\):将数据框添加到搜索路径，表示遇到一个变量名后，将检查路径中的数据框，检查该变量是否已经存在于某个数据框，就不需要$来索引了；但当原环境已经有了一个相同名称的原变量，原变量将取得优先权；此时可采用with\(\)函数

【应尽量避免使用attach；变量名较短、重复次数较少时直接使用$;或直接说明数据来源，即添加参数data=data\_name;使用with\(\)或与之类似的within\(\)】

例子：

attach\(data\.frame\_name\)

plot\(mpg,disp\)\#以mtcars为例，在红色句子中就不用mtcars$mpg或mtcars$disp

detach\(data\.frame\_name\)\#将数据框从搜索路径中删除

例子：

with\(mtcars,\{

print\(summary\(mpg\)\)

  plot\(mpg,disp\)

\}\)：对\{\}内的每条语句都是对mtcars执行，如果仅有一条语句，\{\}可省略，但是with\(\)内的赋值仅在此函数内生效，无法在global\_env中找到此中赋值的对象。此时可以将赋值符号换成<<\-,它可以将对象保存到with\(\)之外的global\_env中

使用\[\]和\[\[\]\]来取出其中的数据分别以数据框或vector显示，前者比喻成从火车中取出的一节车厢加车头，可以运行（为数据框），后者只是车厢，不能运行

实例标识符：

可通过数据框操作函数中的rowname参数选项指定，用于区分数据集中不同的个体，如病例数据中的病人编号：             

data,frame\(patientID,age,diabetes,row\.names=patientID\)\#将patientID指定为R中标记各类打印输出和图形中实例名称所用的变量

rownames\(\):查看所有行名

colnames\(\):查看所有列名

#### <a id="_列表_1"></a>列表

list\(name1=object1,name2=object2,object3,\.\.\.\)：name可以省略，object指任意结构

列表访问：

list\_name\[n\]:访问第一个列（元素）

list\_name\[c\(\)\]:访问c（）的列（元素）

list\_name\[c\(“name1”,”name2”\)\]：通过名称访问

list\_name$name:访问name这个元素

list\_name\[\[n\]\]:与list\_name\[n\]不一样，输出的是第n个元素本身的数据结构，而后者数据结构是list；也可以吧n换成”name”

list\_name\[\[n\]\]<\-x:给list中第n个元素修改成x；删除就是利用负索引或NULL

#### 因子

函数factor\(\)以一个整数向量的形式存储类别值，整数的取值范围是\[1\.\.\.k\]\(k是名义型变量中唯一值的个数\)，同时一个由字符串\(原始值\)组成的内部向量将映射到这些整数上：

plot\(mtcars$cyl\)：显示为散点图

plot\(factor\(mtcars$cyl\)\)：显示为柱状图

普通因子：

factor\(x\):将向量x存储为名义型变量的因子，具体赋值根据字母顺序而定

有序因子：

factor\(x,ordered=TRUE\):将向量x存储为有序型变量的因子。

如果x是字符型vector，各水平排序顺序根据字母顺序而定；或者通过指定levels来覆盖默认排序：

factor\(x,ordered=TRUE,levels=c\(“”,””\)\):各水平排序顺序则是修改为c\(“”,“”\)中所指定的顺序；若x有，但未在参数中列举的元素将被设置为缺失值。

如果x是数值型量：

factor\(x,levels=c\( , \),labels=c\(“”,””\)\):标签的顺序和水平一致，levels中的数值是数值型变量x中有的值，同样x有但不在levels中的将被设为缺失值；labels中的字符串将代替原数值输出

cut\(x,c\(seq\(a,b,n\)\)\)：将向量x按照从a\-b，每间隔n作一个区间对x进行分类

### python统计函数

包含一些数据中的统计量以及统计模型

#### 统计量相关
sd\(\):标准差

cor\(\):求数据之间的相关度

table\(\):进行频数统计，或生成列联表

#### 统计模型及算法

lm\(\):拟合回归模型，如lmfit <\- lm\(mpg~wt,data=mtcars\)，对mtcars数据集中的mpg和wt两个做拟合

summary\(lmfit\):分析结果的统计概要

cooks\.diatance\(\):计算和保存影响度量统计量，此处选择Cook距离作为度量影响的统计量

predict\(\):预测

## 快捷键

仅常用快捷键，更多可参考[快捷键](http://blog.sina.com.cn/s/blog_403aa80a0101ar8q.html)

### 控制台区（console）

功能

Windows & Linux

Mac

移动鼠标到控制台

Ctrl\+2

Ctrl\+2

控制台清屏

Ctrl\+L

Command\+L

移动鼠标至第一行

Home

Command\+Left

移动鼠标至最后一行

End

Command\+Right

历史记录翻滚

Up/Down

Up/Down

弹出历史记录栏

Ctrl\+Up

Command\+Up

终止目前命令

Esc

Esc

改变工作路径

Ctrl\+Shift\+K

Ctrl\+Shift\+K

### 工作区（代码区）

功能

Windows & Linux

Mac

移动鼠标到文件搜索区

Ctrl\+\.

Ctrl\+\.

移动鼠标至文件编辑区

Ctrl\+1

Ctrl\+1

新文档（chrome除外）

Ctrl\+Shift\+N

Command\+Shift\+N

打开文档

Ctrl\+O

Command\+O

保存当前文档

Ctrl\+S

Command\+S

关闭当前文档（chrome除外\)

Ctrl\+W

Command\+W

关闭当前文档（chrome）

Ctrl\+Shift\+Z

Command\+Shift\+Z

关闭所有文档

Ctrl\+Shift\+W

Command\+Shift\+W

插入块

Ctrl\+Alt\+I

Command\+Option\+I

插入代码块

Ctrl\+Shift\+R

Command\+Shift\+R

运行当前行

Ctrl\+Enter

Command\+Enter

重新运行当前区

Ctrl\+Shift\+P

Command\+Shift\+P

运行当前文档

Ctrl\+Alt\+R

Command\+Option\+R

从开始运行至当前行

Ctrl\+Alt\+B

Command\+Option\+B

从当前行运行至结尾

Ctrl\+Alt\+E

Command\+Option\+E

运行当前函数定义

Ctrl\+Alt\+F

Command\+Option\+F

运行当前块

Ctrl\+Alt\+C

Command\+Option\+C

运行下一个块

Ctrl\+Alt\+N

Command\+Option\+N

执行一个文件

Ctrl\+Shift\+O

Command\+Shift\+O

执行当前文档

Ctrl\+Shift\+S

Command\+Shift\+S

执行当前文档并且显示

Ctrl\+Shift\+Enter

Command\+Shift\+Enter

前往行

Shift\+Alt\+G

Command\+Shift\+Option\+G

跳至

Shift\+Alt\+J

Command\+Shift\+Option\+J

换页

Ctrl\+Alt\+Down

Ctrl\+Option\+Down

往回导航

Ctrl\+F9

Command\+F9

向前导航

Ctrl\+F10

Command\+F10

行缩进

Ctrl\+I

Command\+I

注释/取消注释当前行

Ctrl\+Shift\+C

Command\+Shift\+C

重新注释

Ctrl\+Shift\+/

Command\+Shift\+/

## R报错

1. non\-numeric argument to binary operator

该报错显示出现即表达矩阵里面含有非数值型变量的character\(字符\)

解决方法一：把数据类型转化成numeric型，代码例子如下:

mydata1\[,c\(3:7\)\] <\- as\.numeric\(unlist\(mydata1\[,c\(3:7\)\]\)\)

解决方法二：删除\(某列）非numeric数据

二、Error in make\.names\(col\.names, unique = TRUE\) : 

  invalid multibyte string 1

解决方法：\.csv中有中文，可以加一个encoding = "UTF\-8"，如

bio<\-read\.csv\("bio\_1\.csv",encoding = "UTF\-8"\)

三、warning message：程辑包’xxxx’是用R版本4\.1\.3来建造的

不是报错，它建议你进行更新，不然会有问题，一般忽略它就行。