# 基本统计分析

包含描述性统计分析、频数表和列联表、相关系数和协方差、t检验、非参数统计

## 描述性统计分析

### 基础安装包中描述性统计量的计算方法：

第一种：

summary\(\)函数提供了最小值、最大值、四分位数和数值型变量的均值，以及因子向量和逻辑型向量的频数统计。

例：

vars <\-c\("mpg", "hp", "wt"\)

summary \(mtcars \[vars\]\)

第二种：

apply（）函数或sapply（）函数计算所选择的任意描述性统计量。对于sapply（）函数，其使用格式为：

sapply（x，FUN，options）

其中的x是你的数据框（或矩阵），FUN为一个任意的函数。如果指定了options，它们将被传递给FUNV，你可以在这里插入的典型函数有mean，sd，var，min，max，median，length，range和quantile以此得出相应的统计量。

第三种：

函数fivenum（）可返回图基五数总括（Tukey's five\-number summary，即最小值、下四分位数、中位数、上四分位数和最大值）。

但是基础安装包中并没有提供偏度和峰度的计算函数，不过你可以自行添加。

例：

mystats <\-function\(x, na\.omit=FALSE\)\{

  if \(na\.omit\)

    x<\-x\[\!is\.na\(x\)\]

  m <\-mean \(x\)

  n \-length \(x\)

  s <\-sd\(x\)

  skew <\-sum \(\(x\-m\) ^3/s^3\)/n

  kurt <\-sum\(\(x\-n\)^4/s^4\)/n\-3

  return \(c\(n=n, mean=m, stdev=s, skew=skew, kurtosis=kurt\) \)

\}

myvars <\- c\("mpg","hp","wt"\)

sapply\(mtcars\[myvars\],mystats\)

【如果你只希望单纯地忽略缺失值，那么应当使用sapply（mntcars\[myvars\]，mystats，na\.omit=TRUE）】

### 利用载入包中函数

若干用户贡献包都提供了计算描述性统计量的函数，其中包括Hmisc、pastecs和psych。

Hmisc包中的describe（）函数可返回变量和观测的数量、缺失值和唯一值的数目、平均值、分位数（0\.05\-0\.95），以及五个最大的值和五个最小的值。

例子：

library\(Hmisc\)

myvars <\- c\("mpg","hp","wt"\)

describe\(mtcars\[myvars\]\)

pastecs包中有一个名为stat\.desc（）的函数，它可以计算种类繁多的描述性统计量。使用格式为：

stat\.desc（x，basic=TRUE，desc=TRUE，norm=FALSE，p=0\.95）

其中的x是一个数据框或时间序列。若basic=TRUE（默认值），则计算其中所有值、空值、缺失值的数量，以及最小值、最人值、值域，还有总和。

若desc=TRUE（同样也是默认值），则计算中位数、平均数、平均数的标准误、平均数置信度为95%的置信区间、方差、标准差以及变异系数。最后，若norm=TRUE（不是默认的），则返回正态分布统计量，包括偏度和峰度（以及它们的统计显著程度）和Shapiro\-Wilk正态检验结果。这里使用了p值来计算平均数的置信区间（默认置信度为0\.95）。

例子：

library \(pastecs\)

myvars <\- c\("mpg","hp","wt"\)

stat\.desc \(mtcars\[myvars\]\)

psych包也拥有一个名为describe（）的函数，它可以计算非缺失值的数量、平均数、标准差、中位数、截尾均值、绝对中位差、最小值、最大值、值域、偏度、峰度和平均值的标准误。

例子：

library \(psych\)

myvars <\- c\("mpg","hp","wt"\)

describe\(mtcars\[myvars\]\)

【若先后载入的两个包中含有同名函数，后载入的包优先，前一个包含数仍存在，可以使用Hmisc: : describe \(mtcars\[myvars\]\)来使用】

### 分组计算描述性统计量：

在比较多组个体或观测时，关注的焦点经常是各组的描述性统计信息，而不是样本整体的描述性统计信息。

vars <\- c\("mpg","hp","wt"\)

aggregate\(mtcars \[vars\], by=list \(am=mtcars$am\), mean\)

aggregate\(mtcars \[vars\], by=list \(am=mtcars$am\) , sd\)

\#by=list \(namel=groupvar1, name2=groupvar2, \.\.\. , groupvarN\)

\#以am这个0\-1向量为分类，求mpg、hp、wt三水平的均值及标准差

但是，aggregate（）仅允许在每次调用中使用平均数、标准差这样的单返回值函数，无法一次返回若干个统计量。要完成这项任务，可以使用by（）函数。格式为：

by（data，INDICES，FUN）

其中data是一个数据框或矩阵，INDICES是一个因子或因子组成的列表，定义了分组，FUN是任意函数。

例子：

dstats <\- function\(x\)sapply\(x,mystats\)\#如果function只有一个语句，不需要括号；mystats是描述性统计自定义函数；旧版中c\(mean=mean\(x\) , sd=sd\(x\)\)无法运行可能是由于现在版本的R中mean和sd函数不能用于数据框的缘故。

vars <\- c\("mpg","hp","wt"\)

by\(mtcars\[vars\], mtcars$am, dstats\)

### 分组计算的扩展：

doBy、psych包也提供了分组计算描述性统计量的函数。

doBy包中summaryBy（）函数的使用格式为：

summaryBy（formula，data\-dataframe，FUN\-function）

其中的formula接受以下的格式：

var1 \+ var2 \+ var3 \+\.\.\.\+ varN~groupvar1 \+ groupvar2\+\.\.\+ groupvarN

在~左侧的变量是需要分析的数值型变量，而右侧的变量是类别型的分组变量；function可为任何内建或用户自编的R函数。

例：

library \(doBy\)

summaryBy\(mpg\+hp\+wt~am, data=mtcars, FUN=mystats\)

\#分别列出以am为分类变量时，以mpg、hp、wt三组数值变量的描述性统计量

   psych包中的describeBy（）函数可计算和describe相同的描述性统计量，只是按照一个或多个分组变量分层。

例：

library \(psych\)

vars <\- c\("mpg","hp","wt"\)

describeBy\(mtcars\[vars\], mtcars$am\)

\#同样地，以am为分类变量时，以mpg、hp、wt三组数值变量的描述性统计量

【describe\.by（）函数不允许指定任意函数，所以它的普适性较低。若存在一个以上的分组变量，你可以使用list（groupvar1，groupvar2，\.\.\.，groupvarn）

来表示它们。但这仅在分组变量交叉后不出现空白单元时有效。】

    reshape包中cast\(\)函数也可以使用函数，和melt\(\)一起完成分组计算

数据的可视化：

利用前述的各图来洞悉那些依赖于观察一小部分描述性统计量时忽略的细节。

## 频数表和列联表

目前我们考虑的函数都是为定量变量提供概述的，本节将考察类别型变量的分布。

着眼于类别型变量的频数表和列联表，以及相应的独立性检验、相关性的度量、图形化展示结果的方法。

### 生成频数表

我们将使用table\(\)或xtabs\(\)函数创建一个表，然后使用其它函数处理它。


#### 一维列联表

例子：

library\(vcd\)

mytable <\- with\(Arthritis, table\(Improved\)\)\#生成简单的一维列联表

prop\.table\(mytable\)\#将各频数转化为比例值

prop\.table\(mytable\)\*100\#转化为百分比

#### 二维列联表

第一种：

table\(\)函数的使用格式为：

mytable <\-table（A，B）

其中的A是行变量，B是列变量。

第一种：

除此之外，xtabs\(\)函数还可使用公式风格的输入创建列联表，格式为：

mytable <\-xtabs（~A\+B，data=mydata）

mydata是一个矩阵或数据框。同样A是行变量，B是列变量。同样地，要进行交叉分类的变量在符号的右方，以\+作为分隔符。若某个变量写在公式的左侧，则其为一个频数向量（在数据已经被表格化时很有用）。

例子：

mytable <\-xtabs\(~Treatment\+Improved,data=Arthritis\)\#以Treatment为行变量，以Improved为列变量

margin\.table\(mytable, 1\)\#1代指原table\(\)或xtabs\(\)语句中第一个变量，即以Treatment分类计算表中条目的和\(即边际频数，行和\)，或称为Treatment生成边际数

prop\.table\(mytable,1\)\#以Treatment分类计算表中条目的比例（行比例）

prop\.table\(mytable\)\#显示所有单元格所占比例 

 

addmargins\(\)函数为这些表格添加边际和：

addmargins\(mytable\)\#添加各行各列的边际和

addmargins\(prop\.table\(mytable\)\)\#添加计算出各单元格比例的边际和

addmargins\(prop\.table\(mytable,1\),2\)\#以第一个分类变量计算比例，最后计算各行之和输出到新的一列

addmargins\(prop\.table\(mytable,2\),1\)\#同理以第二个分类变量计算比例，最后求和输出到新的一行

【table\(\)函数默认忽略缺失值（NA），要在频数统计中将NA视为一个有效的类别，请设定参数useNA="ifany"】

第二种：

使用gmodels包中的CrossTable\(\)函数是创建二维列联表的第三种方法

library \(gmodels\)

CrossTable\(Arthritis$Treatment, Arthritis$Improved\)

\#一次性展示以第一变量、第二变量和所有变量的比例

CrossTable（）函数有很多选项，可以做许多事情：计算（行、列、单元格）的百分比；指定小数位数；进行卡方、Fisher和MCNemar独立性检验；计算期望和（皮尔逊、标准化、调整的标准化）残差；将缺失值作为一种有效值；进行行和列标题的标注；生成SAS或SPSS风格的输出。

#### 三维列联表

table\(\)和xtabs\(\)、margin\.table\(\)、prop\.table\(\)和addmargins\(\)函数可以自然地推广到高于二维的情况。另外，ftable\(\)函数可以以一种紧凑而吸引人的方式输出多维列联表。

例子：

with\(Arthritis,table\(Treatment,Sex,Improved\)\)

mytable <\- xtabs \(~Treatment\+Sex\+Improved, data=Arthritis\)

ftable\(mytable\) 

margin\.table\(mytable,1\)\#为第一个变量Treatment生成边际数

margin\.table\(mytable,c\(1,3\)\)\#治疗情况（Treatment）\*改善情况（Improved）为分类变量分组，计算Improved的边际频数

ftable\(prop\.table\(mytable,c\(1, 2\)\)\)

\#Treatment\*Sex分组计算Improved为None,Some,Marked的比例

ftable\(addmargins\(prop\.table\(mytable, c\(1, 2\)\), 3\)\)

\#其添加比例，若想得到百分比,最终结果表格乘以100

【addmargins来添加比例的参数总是将被添加到不在prop\.table\(\)调用中的下标上】

### 独立性检验

#### 卡方独立性检验

使用chisq\.test（）函数对二维表的行变量和列变量进行卡方独立性检验。

原假设是两变量相互独立

例：

library\(vcd\)

mytable <\-xtabs\(~Treatment\+Improved, data=Arthritis\)

chisq\.test\(mytable\)\#结果中的p值表示从总体中抽取的样本行变量与列变量是相互独立的概率。故越小\(p<0\.01\)表示相互独立的概率小；越大\(p>0\.05\)表示相互独立的概率。

#### Fisher检验

使用fisher\.test（）函数进行Fisher精确检验。

原假设是：边界固定的列联表中行和列是相互独立的。其调用格式为：

fisher\.test（mytable）

其中的mytable是一个二维列联表。

例:

mytable <\-xtabs\(~Treatment\+Improved, data=Arthritis\)

fisher\.test \(mytable\)

【与许多统计软件不同的是，这里的fisher\.test（）函数可以在任意行列数大于等于2的二维列联表上使用，但不能用于2 x2的列联表】

#### Cochran\-Mantel\-Haenszel检验：

mantelhaen\.test（）函数可用来进行Cochran\-Mantel\-Haenszel卡方检验

原假设是，两个名义变量在第三个变量的每一层中都是条件独立的。

例：

mytable <\-xtabs\(~Treatment\+Improved\+Sex, data=Arthritis\)

\#检验治疗情况和改善情况在性别的每一水平下是否独立

mantelhaen\.test\(mytable\)

【此检验假设不存在三阶交互作用（治疗情况\*改善情况?\*性别）】

### 相关性检验

如果经显著性检验拒绝原假设，即变量间不相互独立，我们将要衡量相关性强弱。vcd包中的assocstats（）函数可以用来计算二维列联表的phi系数、列联系数和Cramer's V系数。

例子：

library\(vcd\)

mytable <\- xtabs\(~Treatment\+Improved, data=Arthritis\)

assocstats\(mytable\)

总体来说，较大的值意味着较强的相关性。

【vcd包也提供了一个kappa（）函数，可以计算混淆矩阵的Cohen'skappa值以及加权的kappa值（举例来说，混淆矩阵可以表示两位评判者对于一系列对象进行分类所得结果的一致程度。）】

     结果的可视化：

条形图进行一维频数的可视化；vcd包中的马赛克图和关联图；ca包中的对数分析函数

## 相关：（the last is skipped）

相关系数可以用来描述定量变量之间的关系。相关系数的符号（±）表明关系的方向（正相关或负相关），其值的大小表示关系的强弱程度（完全不相关时为0，完全相关时为1）

本节中，我们将关注多种相关系数和相关性的显著性检验。

### 相关的类型：

R可以计算多种相关系数，包括Pearson相关系数、Spearman相关系数、Kendall相关系数、偏相关系数、多分格（polychoric）相关系数和多系列（polyserial）相关系数

1. Pearson，Spearman和Kendall相关

Pearson积差相关系数衡量了两个定量变量之间的线性相关程度。Spearman等级相关系数则衡量分级定序变量之间的相关程度。Kendall's Tau相关系数也是一种非参数的等级相关度量。

cor（）函数可以计算这三种相关系数，而cov（）函数可用来计算协方差。两个函数的参数有很多，其中与相关系数的计算有关的参数可以简化为：

cor（x，use=，method=）


默认参数为use="everything"和method="pearson”

例子：

states<\-state\.x77\[,1:6\]

cov\(states\)

cor\(states\)

cor\(states, method= "spearman"\)

默认是所有变量两两之间计算相关，也可以自己选定：

x <\- states\[,c\("Population","Income","Illiteracy","HS Grad"\)\]

y <\- states\[,c\("Life Exp", "Murder"\)\]

cor\(x,y\)

【上述结果并未指明相关系数是否显著不为0（即，根据样本数据是否有足够的证据得出总体相关系数不为0的结论），需要对相关系数进行显著性检验】

1. 偏向关：

偏相关是指在控制一个或多个定量变量时，另外两个定量变量之间的相互关系。你可以使用ggm包中的pcor（）函数计算偏相关系数。函数调用格式为：

pcor（u，s）

u是一个数值向量，前两个数值表示要计算相关系数的变量下标，其余的数值为条件变量（即要排除影响的变量）的下标。s为变量的协方差阵。

例：

library\(ggm\)

colnames\(states\)

pcor\(c\(1,5,2,3,6\),cov\(states\)\)

\#在控制了收入、文盲率和高中毕业率的影响时，人口和谋杀率之间的相关系数为0\.346，偏相关系数常用于社会科学的研究中。

1. 其它类型的相关：

polycor包中的hetcor（）函数可以计算一种混合的相关矩阵，其中包括数值型变量的Pearson积差相关系数、数值型变量和有序变量之间的多系列相关系数、有序变量之间的多分格相关系数以及二分变量之间的四分相关系数。多系列、多分格和四分相关系数都假设有序变量或二分变量由潜在的正态分布导出。

### 相关性的显著性检验

在计算好相关系数以后，对它们进行统计显著性检验。常用的原假设为变量间不相关（即总体的相关系数为0），你可以使用cor\.test（）函数对单个的Pearson、Spearman和Kendall相关系数进行检验。简化后的使用格式为：

cor\.test（x，y，alternative =，method =）

其中的x和y为要检验相关性的变量，alternative则用来指定进行双侧检验或单侧检验（取值为"two\.side"，"less"或"greater"），而msthad用以指定要计算的相关类型（"pearson"\."kendall"或"spearman"）;当研究的假设为总体的相关系数小于0时，请使用alternative="less",在研究的假设为总体的相关系数大于0时，应使用alternative="greater",默认为“two\.side”\(总体相关系数不等于0\)

例子：

cor\.test\(states\[,3\],states\[,5\]\)

【cor\.test\(\)每次只能检验一种相关关系】

psych包中corr\.test（）函数可以一次做更多事情。corr\.test（）函数可以为Pearson、Spearman或Kendall相关计算相关矩阵和显著性水平。

例子：

library \(psych\)

corr\.test\(states, use="complete"\)

参数use=的取值可为"pairwise"或"complete"（分别表示对缺失值执行成对删除或行删除），参数method=的取值可为"pearson"（默认值）、"spearman"或"kendall"。

其它显著性检验：

（skip）

    

相关关系的可视化：

散点图、散点图矩阵、相关图

       

## t检验：

### 独立样本的t检验：

t\.test（y1，y2）

其中的y1和y2为数值型向量（即各组的结果变量）

### 非独立样本的t检验：

t\. test \(yl, y2, paired=TRUE\)

多于两组的情况：方差分析ANOVA

## 组间差异的非参数检验

### 两组的比较：

若两组数据独立，可以使用wilcoxon秩和检验（更广为人知的名字是Mann\-Whitney U检验）来评估观测是否是从相同的概率分布中抽得的（即，在一个总体中获得更高得分的概率是否比另一个总体要大）。调用格式为：wilcox\.test（y ~x，data）

其中的y是数值型变量，而x是一个二分变量。调用格式或为：

wilcox\.test（y1，y2）

### 多于两组的比较：

Kruskal\-Wallis检验

Friedman检验

导入Excel文件

或者使用gdata包

矩阵是同一类型向量集合，列表是不同类型数据集合，数据框是不同向量类型集合

I have one question how can i remember the words of the options in every function? and is it necessary to remember them or u can check it by searching it?

某些缩写难记
