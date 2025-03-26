# 高级数据管理

包括数学和统计函数、字符处理函数、循环和条件执行、自编函数、数据整合与重塑

任务：要确定单一的成绩指标；并分别评定等级；最后按字母排序


## 数值和字符处理函数

### 常用数学函数


abs\(absolute\)  sqrt\(square root\)  ceiling\(上限，天花板\)

trunc\(truncate截断\)  digits数字

这些函数作用于数值向量、矩阵或数据框时，它们会作用于每一个独立的值。

### 常用统计函数

sd\(standard deviation偏差\)  var\(variance方差\)  sum\(summation\)

有很多都拥有可选参数；如trim，na\.rm等

如：

z <\-mean\(x, trim =0\.05, na\. rm=TRUE）\#提供了截尾平均数，即丢弃了最大5%和最小5%的数据和所有缺失值后的算术平均数。

#### 数据的标准化

newdata <\-scale（mydata）\#默认情况下对矩阵或数据框的指定列进行均值为0、标准差为1的标准化

要对每一列进行任意均值和标准差的标准化，可以使用如下的代码：

newdata <\-scale（mydata）\*SD \+M\#其中的M是想要的均值，sD为想要的标准差。在非数值型的列上使用scale（）函数将会报错。

要对指定列而不是整个矩阵或数据框进行标准化，你可以使用这样的代码：

newdata <\-transform（mydata，myvar = scale（myvar）\*10\+50）\#指定列进行标准化，将变量myvar标准化为均值50、标准差为10的变量

### 常用概率函数

概率函数通常用来用来生成特征已知的模拟数据，以及在用户编写的统计函数中计算概率值。


概率函数使用格式为：

d/p/q/r\+distribution\_abbreviation\(分布\_缩写\)3

例：

x <\- pretty\(c\(\-3,3\),10\)\#分成10个区间，即11个值，但因为无法整除，最终变成13个值以达到美观的目的

y <\- dnorm\(x\)\#正态分布密度函数

plot\(x,y,type="l",yaxs="i"\)\#曲线贴近x轴

pnorm\(1\.98\)\#z=1\.98左侧的标准正态曲线下方面积

qnorm\(\.9,mean=500,sd=100\)\#均值为500，标准差为100的正态分布的0\.9分位点值

__rnorm__\(50,mean=50,sd=10\)\#生成均值为50，标准差为10的正态随机数

#### 设定随机数种子

在每次生成\(伪\)随机数的时候，函数都会使用一个不同的种子，也就会产生不同的结果。你可以通过函数set\.seed（）显式指定这个种子，让结果可以重现（reproducible）。代码清单5\-2给出了一个示例。这里的函数runif（）用来生成0到1区间上服从均匀分布的伪随机数\(random samples from uniform distribution\)

例：

set\.seed\(12\)\#该随机数的符号标记为12，下一次产生参数时也应该运行这一句；

runif\(5\)

runif\(5\)\#前三句创建一个包含两个随机数的种子，

set\.seed\(12\)\#使用该随机数种子

runif\(5\)

runif\(6\)\#超出的部分重新生成随机数

【通过手动设定随机数种子，就可以重现结果】

#### 生成多元正态数据

利用MASS包中的mvrnorm（）函数可以获取来自给定均值向量和协方差阵的多元正态分布数据：

mvrnorm（n，mean，sigma）

其中n是你想要的样本大小，mean为均值向量，而sigma是方差\-协方差矩阵（或相关矩阵）。

例：略

### 字符处理函数

而字符处理函数可以从文本型数据中抽取信息，或者为打印输出和生成报告重设文本的格式。举例来说，你可能希望将某人的姓和名连接在一起，并保证姓和名的首字母大写，抑或想统计可自由回答的调查反馈信息中含有秘语的实例

（instance）数量。一些最有用的字符处理函数：


请注意，函数grep（）、sub（）和strsplit（）能够搜索某个文本字符申（fixed\-TRUE）或某个正则表达式（fixed=FALSE，默认值为FALSE）

【这个记得了解】

### 其它实用函数


表中的最后一个例子演示了在输出时转义字符的使用方法。\\n表示新行，\\t为制表符，\\’为单引号，\\b为退格，等等。（？Quotes以了解更多）

【当cat输出连接后的对象时，它会将每一个对象都用空格分开】

## 函数应用

### 将函数应用于矩阵和数据框

R函数的特性之一就是可以应用到一系列的数据对象上，包括标量、向量、矩阵、数组和数据框。R还提供了一个函数apply\(\)，可将一个任意函数“应用”到矩阵、数组、数据框的任何维度上，使用格式为：

apply（x，MARGIN，FUN，\.\.\.）

其中，x为数据对象，MARGIN是维度的下标，FUN是由你指定的函数，而\.\.\.则包括了任何想传递给FUN的参数。在矩阵或数据框中，MARGIN=1表示行，MARGIN=2表示列。

例：

apply \(mydata, 2, mean, trim=0\.2\)\#计算某矩阵mydata每列的结尾均值（基于中间60%）

FUN可为任意R函数，也包括自编函数，所以apply（）是一种很强大的机制。apply（）可把函数应用到数组的某个维度上，而lapply（）和sapply（）（sapply是lapply的更好用的版木）则可将函数应用到列表（list）上。

 

### 数据处理难题的一套解决方案

5\.1节中提出的问题是：将学生的各科考试成绩组合为单一的成绩衡量指标、基于相对名次（前20%，下20%，等等）给出从A到F的评分、根据学生姓氏和名字的首字母对花名册进行排序。

digits=2：对于小数（无论是向量还是标量），保留到两位有效数字；大于两位有效数字的整数不限；对于有整数部分是0的，则保留两位小数（其实也就是两位有效数字，但如果对于在向量中只要有一个数的整数部分为0的，则其余也按照保留两位小数而不是保留两位有效数字）

sapply\(list, function\)\#sapply返回的是一个vector

代码：

options\(digits = 2\)\#使输出更容易阅读

\#输入该数据框

z <\- scale\(roster\[,2:4\]\)\#标准化，使可以比较

score <\- apply\(z,1,mean\)\#计算各行的均值获得综合得分

roster <\- cbind\(roster,score\)\#将综合得分添加到原花名册

y <\- quantile\(score,c\(\.8,\.6,\.4,\.2\)\)\#计算分位数

roster$grade\[score >= y\[1\]\] <\-"A"

roster$grade\[score < y\[1\] & score >= y\[2\]\] <\-"B"

roster$grade\[score < y\[2\] & score >= y\[3\]\] <\-"c"

roster$grade\[score < y\[3\] & score >= y\[4\]\] <\-"D"

roster$grade\[score < y\[4\]\] <\- "F"

name <\- strsplit\(\(roster$Student\),split = " "\)

\#对姓名进行分割，每遇到空格就设为一个字符,应用到字符向量返回一个列表

Lastname <\- sapply\(name,"\[",2\)

\#"\["是一个可以提取某个对象的一部分的函数

Firstname <\- sapply\(name,"\[", 1\)

\#在这里\\用来提取列表name各成分中的第一个或第二个元素

roster <\- cbind\(Firstname, Lastname, roster\[,\-1\]\)

\#使用cbind\(\)把它们添加到花名册中,并将student变量丢弃（下标\-1）

roster\[order\(Lastname,Firstname\),\]

\#依据姓名和名字对数据集进行排序

## 控制流

三个概念：

语句（statement）是一条单独的R语句或一组复合语句（包含在花括号\{\}中的一组R语句，使用分号分隔）；

条件（cond）是一条最终被解析为真（TRUE）或假（FALSE）的表达式；

表达式（expr）是一条数值或字符串的求值语句；

序列（seq）是一个数值或字符串序列

### 重复和循环

循环结构重复地执行一个或一系列语句，直到某个条件不为真为止

1. for循环：

for循环重复地执行一个语句，直到某个变量的值不再包含在序列seq中为止。语法为：

for（var in seg）

statement

执行满足for\(\)次数的statement

2\.while结构：

while循环重复地执行一个语句，直到条件不为真为止。语法为：

while（cond）

statement

只要满足while\(\)中的条件，都将运行statement

### 条件执行

在条件执行结构中，一条或一组语句仅在满足一个指定条件时执行

1. if\-else结构：

if \(cond\) 

statement

\#如果满足if\(\)中条件，执行statement

if \(cond\) 

statement1 

else 

statement2

\#满足if\(\)条件，执行语句1，不满足执行语句2

if \(cond1\) 

statement1 else if\(cond2\)

statement2

1. ifelse结构：

ifelse结构是if\-else结构比较紧凑的向量化版本，其语法为：

ifelse（cond，statement1，statement2）

\#若cond为TRUE，则执行语句1；若cond为FALSE，则执行语句2;也可以嵌套

【在程序的行为是二元时，或者希望结构的输入和输出均为向量时，请使用ifelse】

1. switch结构：

switch根据一个表达式的值选择语句执行。语法为：

switch（expr，\.\.）

\#\.\.\.表示与expr的各种可能输出值绑定的语句（不仅是数值）

## 自编函数

使用格式：

myfunction\_name <\-function（arg1，arg2，\.\.\.\.）\{

statements 

return（obiect）\#根据需要调用返回值给主函数

\}

函数中的对象只在函数内部使用。返回对象的数据类型是任意的，从标量到列表皆可，

例子：

\#在函数声明中为参数指定的值将作为其默认值。在函数mydate（）中，如果未指定type，long将为默认的日期格式：

mydate <\-function\(type="long"\)\{

  switch\(type,

  long = format\(Sys\.time\(\),"%A %B %d %Y"\),

  short = format\(Sys\.time\(\),"%m\-%d\-%y"\),

  cat\(type,"is not a recognized type\\n"\)

  \)\#cat来显示错误输入的参数

\}

【有若干函数可以用来为函数添加错误捕获和纠正功能。你可以使用函数warning（）来生成一条错误提示信息，用message（）来生成一条诊断信息，或用stop（）停止当前表达式的执行并提示错误】

## 整合与重构

在整合\(aggregate\)数据时，往往将多组观测替换为根据这些观测计算的描述性统计量。在重塑\(reshape\)数据时，则会通过修改数据的结构（行和列）来决定数据的组织方式

### 转置

使用函数t（）即可一个矩阵或数据框进行转置。对于后者，行名将成为变量（列）名

逆矩阵：solve（）

### 整合数据

在R中使用一个或多个by变量和一个预先定义好的函数来折叠（collapse）数据是比较容易的调用格式为：

aggregate（x，by，FUN）

其中x是待折叠的数据对象，by是一个变量名组成的列表，这些变量将被去掉以形成新的观测，而FUN则是用来计算描述性统计量的标量函数，它将被用来计算新观测中的值。

【根据by里list包含变量对x中变量进行相应计算，先进行一个根据list进行分类的过程后再执行函数FUN】

例子：

mtcars

attach\(mtcars\)

aggdata <\- aggregate\(mtcars,by=list\(cyl,gear\),FUN=mean,na\.rm=T\)

\#按照cyl和gear两个条件求出个变量的均值，也可以自定义新生成分类的名称，如：

by=list（Group\.cyl=cyl，Group\.gears=gear）

### reshape2包\(重构与整合数据集\)

大致说来，你需要首先将数据“融合”（melt），以使每一行都是一个唯一的标识符\-变量组合。然后将数据“重铸”（cast）为你想要的任何形状。在重铸过程中，你可以使用任何函数对数据进行整合。

如上数据集，测量（measurement）是指最后两列中的值，每个测量都能够被标识符变量（在本例中，标识符是指ID、Time以及观测属于X1还是X2）唯一地确定。举例来说，在知道ID为1、Time为1，以及属于变量X1之后，即可确定测量值为第一行中的5。

1. 融合：

数据集的融合是将它重构为这样一种格式：每个测量变量独占一行，行中带有要唯一确定这个测量所需的标识符变量。

例子：

library（reshape）

md <\-melt（mydata，id=（c（"ID"，"Time"））

\#将ID和Time设为标识符变量\(所有组合\)，共八个测量

1. 重铸

使用dcast（）函数读取已融合的数据，并使用你提供的公式和一个（可选的）用于整合数据的函数将其重铸。调用格式为：

newdata <\-dcast（md，formula，FUN）

其中的md为已融合的数据，formula描述了想要的最后结果，而FUN是（可选的）数据整合函数，其接受的公式formula形如：

rowvar1\+ rowvar2\+\.\.\.~ colvar1 \+ colvar2 \+\.\.\.

该式中，以~左边的为唯一标识符，以~右边的为测量的分类变量，如ID~variable,则生成以ID值作为行的分类变量（2行），以测量为列的分类变量（X1、X2共2行）

【这里的variable只取其字面含义，即仅表示重铸后数据框中的测量变量variable】

【acast\(\)具有类似作用，不过acast获得数组,dcast获得数据框，也就是acast（）没有行标签，获得的可以直接进行各种数值运算】

part2 基本方法
