# 基本数据管理

包括日期型、缺失值、数据转换、变量操作、数据集排序、合并与取子集、选入和丢弃变量这些基本的数据集处理方法

案例：


q为五个问题所填答案

## 变量创建和新编码

### 创建新变量

载入数据（一般是数据框）后，可能需要创建新变量或者对现有变量进行变换并添加到原数据中

变量名<\-表达式\#表达式可以包含多种运算符号和函数


将新变量整合到原始的数据框，可以使用以下三种方法：

例：

mydata<\-data\.frame \(x1 = c\(2, 2, 6, 4\), x2 = c\(3, 4, 2, 8\)\)\#先建立一个原始的数据框

一：

mydata$sumx <\- mydata$x1 \+mydata$x2

mydata$meanx <\-\(mydata$x1 \+ mydata$x2\)/2


1. 三：


### 变量的重编码与重命名

重编码涉及根据一个变量和/或其它变量的现有值创建新值的过程，比如：

将一个连续型变量修改为一组类别\(因子\)值；将误编码的值替换为正确值；基于一组分数线创建一个表示及格或不及格的变量

要重编码数据，可以使用R中的一个或多个逻辑运算符，逻辑运算符表达式可返回TRUE或FALSE


例如：

leadership$age\[leadership$age == 99\]<\-NA\#利用索引将leadership数据集中误编码为99的值替换为缺失值

leadership$age\[leadership$age>75\]<\-“Elder”\#将leadership中age列中大于75的重编码为类别型变量agecat

首先建立agecat变量，并将每一行都设为缺失值，而后依次执行。

但现在的agecat只是一个字符型变量。

若干程序包都提供了实用的变量重编码函数，特别地，car包中的recode（）函数可以十分简便地重编码数值型、字符型向量或因子。而doBy包提供了另外一个很受欢迎的函数recodevar（1）。最后，R中也自带了cut（），可将一个数值型变量按值域切割为多个区间，并返回一个因子。

      

变量的重命名：

第一种，交互地：

fix\(data\.frame\_name\)\#调出交互式界面修改

第二种，运用函数：

names\(data\.frame\_name\)\[number\]<\-“new\_name”

如：names（leadership）\[6：10\]<\-c（"item1","item2","item3","item4","item5"）

\#将重命名数据框leadership中g1到g5为item1到item5

plyr包中含有rename\(\)函数，使用格式为：

rename\(data\.frame\_name,c\(old\_name=”new\_name”,old\_name2=”new\_name2”,\.\.\.\)\)     

      

## 缺失值和日期

### 缺失值（not available，不可用）

与SAS等程序不同，R中字符型和数值型数据使用的缺失值符号是相同的：NA

is\.na\(\)\#检验向量中是否含有缺失值

例：

is\.na\(leadership\[,6:10\]\)\#将数据框限定在第6列至第10列

注意：

1，缺失值不可比较，即无法使用比较运算符来检测缺失值是否存在，只能使用处理缺失值的函数来识别出R数据对象中的缺失值

2，不能将无限的或者不可能出现的数值标记为缺失值，如Inf、\-Inf和NaN\(not a number\),要识别这些需要用到is\.infinite\(\)或is\.nan\(\)

#### 重编码某些值为缺失值

leadership$age\[leadership$age==99\]<\-NA\#将数据框leadership中age列的99值重编码为缺失值

【请确保所有的缺失数据已在分析之前被妥善地编码为缺失值，否则分析结果将失去意义】

#### 在分析中排除缺失值

确定了缺失值的位置以后，你需要在进一步分析数据之前以某种方式删除这些缺失值。原因是，含有缺失值的算术表达式和函数的计算结果也是缺失值。

第一种方法：多数函数都含有参数na\.rm=TRUE参数，在计算前可移除缺失值并使用剩余值进行计算

例：

x<\- c\(1, 2, NA, 3\)

y <\-sum（x，na\.rm=TRUE）\#剔除na值后计算总和

第二种方法：通过na\.omit\(\)函数来删除所有含缺失值数据的行

如：newdata <\-na\.omit \(leadership\)\#删除leadership数据框中含缺失值的所有行

【更多的复杂精妙的缺失值处理方式将在15章中探索】

### 日期值

日期值通常以字符串的形式输入到R中，然后转化为以数值形式存储的日期变量。函数as\.Date（）用于执行这种转化。其语法为:

as\.Date（x，"input\_format"）\#x是字符型数据，input\_format则给出了用于读入日期的适当格式,如：

mydates <\- as\.Date\(c\("2001\-06\-22","2004\-02\-13"\)\)\#class为Date型

strdates <\- c\("01/05/1976","03/09/1987"\)

format<\-"%d/%m/%Y"\#也可以直接输入在函数内

dates <\- as\.Date\(strdates,format\)\#dates格式会被转换成默认日期格式

使用指定格式读取字符型变量，并将其作为一个日期型变量替换到数据框中，你就可以使用后续各章中讲到的诸多分析方法对这些日期进行分析和绘图。

时间戳：

时间戳可以理解为区块的“出厂日期”，它是指从格林威治时间1970年01月01日00时00分00秒起，截止到现在的总秒数，比如3600，表示的就是1970年01月01日01时00分00秒。有两个函数对于处理时间戳数据特别实用：

Sys\.Date（）\#可以返回当天的日期

date（）\#则返回当前的日期和时间

format（x,format=”output\_format”）\#指定输出指定格式的日期值，并且可以提取日期中的某些部分

例：

today <\- Sys\.Date\(\)

format\(today,format="%Y %m %d"\)

format\(today,format="%A"\)

日期的算术运算：

R的内部在存储日期时，是使用自1970年1月1日以来的天数表示的，更早的日期则表示为负数。这意味着可以在日期值上执行算术运算。

例：

startdate <\-as\.Date\("2004\-02\-13"\)

enddate <\-as\.Date\("2011\-01\-22"\)

days<\-enddate\-startdate\#计算出两个日期间隔了多少天

还可以使用difftime\(time1, time2,units = c\("auto", "secs", "mins",\.\.\.\)\)并以星期、天、时、分表示

例：

today <\-Sys\.Date\(\)

dob <\-as\.Date\("1956\-10\-12"\)

difftime\(today, dob, units="weeks"\)

\#返回到现在经历多少个周，还可是days、mins、secs、autos、hours

将日期转换成字符型变量：

strdates<\-as\.character\(dates\)\#将日期型变量dates转换成字符型变量，进行转换后，即可使用一系列字符处理函数来处理数据（如取子集、替换、连接等）

## 类型转换

R与其他统计编程语言有着类似的数据类型转换方式。举例来说，向一个数值型向量中添加一个字符串会将此向量中的所有元素转换为字符型。以下函数可以用来判断数据的类型或者将其转换为指定类型：


前者返回一个逻辑值，后者将函数内参数转换成对应的类型

当和第五章的控制流结合使用，is\.datatype\(\)将允许根据数据类型的具体类型以不同的方式处理数据；as\.datatype\(\)可以将数据转换成某些R函数所需要的格式

## 数据集的排序、合并和取子集     

### 数据排序

查看排序的数据集以获得相当多的信息，使用order\(\)函数对一个数据框进行排序，默认是升序，在变量前加一个负号可为降序。格式为：

newdata<\-data\.frame\_name\[order\(item1,\-item2\),\]\#将data\.frame\_name各行按照item1列分类的前提下，对item2列进行降序

例：

attach\(leadership\)

newdata <\-leadership\[order\(gender, \-age\),\]\#按照性别排序后，对年龄进行降序排列

detach\(leadership\)

三个函数比较：

sort（）是对向量进行从小到大的排序  
rank（）返回的是对向量中每个数值对应的秩  
order（）返回的值表示位置，依次对应的是向量的最小值、次小值、第三小值\.\.\.\.\.\.最大值，常用于索引

      

### 数据集的合并

对于分散的数据，采取合并数据集，向数据框中添加列（变量）和行（观测）

#### 向数据框添加列（变量）

横向合并两个数据框，使用merge\(\)函数，

格式为：

merge\(x, y, by = intersect\(names\(x\), names\(y\)\),all=T,all\.x=T,\.\.\.\)\#x,y为两个要连接的数据框，by = intersect\(names\(x\),names\(y\)\)是获取数据框x，y的列名后，提取其公共列名，作为两个数据集的连接列， 当有多个公共列时，需用下标指出公共列，如names\(x\)\[1\]，指定x数据集的第1列作为公共列。也可直接写成by = “公共列名”，前提是两个数据集中都有该列名，并且大小写完全一致

执行merge函数时，函数自动会找到两个数据框df1和df2共有的列，如ID是个numeric vector。

all=F时，会将两个数据框中该ID列数值相等的那些行合并后一起以一个数据框输出来，多余的行将失去,即求交集；all=TRUE时，会将两数据框中ID这列数值相等的那些行合并后输出来，多余的没有相等值的行也会合并，空白列将以NA补全

all\.x=T时，最终合并的行数将以第一个数据框为准，即不会超过第一个数据框的行数；all\.y=T时，最终合并的数据框函数将以第二个数据框的行数为准。

by参数的使用：

当数据框中有两列甚至多列相同时，by=c\(“item1”,”item2”\)，R会找到item1和item2中共有的值，做交集输出来；如果还有共有列未列出，将会以col\_name\.x和col\_name\.y分别列出两数据框的这两列

不清楚看这个：[merge（）函数合并数据](https://www.jianshu.com/p/e8ca348f7bcb)（第三个by参数的有点奇怪）

total<\- merge \(dataframeA, dataframeB, by="ID"\)

\#按照ID进行合并

如果直接合成两个矩阵或数据框，可直接使用cbind\(\)函数：

cbind\(A,B\)\#A和B必须有相同行数，以同顺序排列

#### 向数据框添加行（观测）

使用rbind\(\)函数：

total<\- rbind \(dataframeA, dataframeB\)\#两个数据框必须拥有相同的变量，不过它们的顺序不必一定相同。

【如果dataframeA中拥有dataframeB中没有的变量，请在合并前做以下某种处理：删除dataframeA中的多余变量；在dataframeB中创建追加的变量并将其值设为NA】

## 数据集取子集

R强大的索引特性，可以用于访问对象中的元素。也可利用这些特性对变量或观测进行选入和排除。

### 选入（保留）变量

从一个大的数据集中选择有限数量的变量创建一个新的数据集：

第一种方法：

newdata <\- leadership\[, c\(6:10\)\]\#将后面五个变量保存到数据框newdata中

第二种方法：

myvars <\-c\("q1", "q2", "q3", "q4 ", "q5"\)

newdata <\-leadership \[myvars\]

第三种方法：

myvars <\- paste\("q", 1:5, sep=""\)

newdata <\- leadership\[myvars\]

### 剔除（丢弃）变量

第一种方法：

myvars <\- names \(leadership\) in% c\("q3","q4"\)\#names\(\)返回一个包含所有变量名的字符型向量；利用%in%检测左边的是否包含右边的，返回一个逻辑向量：对应q3、q4的值为TRUE

newdata <\- leadership \[\!myvars\]\#利用！将逻辑值反转，则最后将q3、q4剔除

第二种方法：

在知道变量的位置时，可直接：

newdata<\-leadership\[c\(\-8,\-9\)\]\#本例中要删除的变量在8,9两列

第三种方法：

leadership$q3 <\- leadership$q4 <\- NULL\#同时将两个设为未定义（NULL）

【丢弃变量是保留变量的逆向操作。选择哪一种方式进行变量筛选依赖于两种方式的编码难易程度。如果有许多变量需要丢弃，那么直接保留需要留下的变量可能更简单，反之亦然。】

### 选入观测

例，选择前三个观测中30岁以上的男性：

newdata <\- leadership\[1:3,\]

newdata <\- newdata\[newdata$gender=="M"&

                     newdata$age>30,\]

选择限定时间段的观测：

例：

leadership$date <\- as\.Date\(leadership$date,"%m/%d/%y"\)\#别忘记/，如果leadershi$date本身就是该函数默认格式yyyy\-mm\-dd，就不用提供格式参数，如：

startdate <\- as\.Date\("2009\-01\-01"\)

enddate <\- as\.Date\("2009\-10\-31"\)

newdata <\- leadership\[which\(leadership$date>=startdate&

                        leadership$date<=enddate\),\]

\#which函数返回的是数值向量，无则返回一个逻辑向量，见[which（）](https://blog.csdn.net/ddxygq/article/details/101351466)

### subset\(\)函数

大概是选择变量和观测最简单的方法：

例：

newdata <\- subset\(leadership,gender=="M" & age > 25, \#选择gender为M且age>25的行

                  select=gender:q4\)\#保留gender到q4及其间所有列

\#选择25岁以上的男性观测值，并保留了变量gender到q4（gender、q4和其间所有列）

### 随机抽样

在数据挖掘和机器学习领域，从更大的数据集中抽样是很常见的做法。比如你希望选择两份随机样本，使用其中一份样本构建预测模型，使用另一份样本验证模型的有效性

例：

mysample <\- leadership\[sample\(1:nrow\(leadership\), 3, replace=FALSE\),\]

\#从该数据框中抽取3个观测，无放回抽样

【R中还含有抽取和校正调查样本的sampling包、分析复杂调查数据的survey包等】

