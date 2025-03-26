# 创建数据集

包括R中的数据结构、数据的输入、数据的导出、数据的标注

## 数据集、数据结构、对象的概念

数据集通常是矩形数组，行表示观测，列表示变量。

R包内置数据集：

data\(\):列出已加载包中所含所有可用实例数据集

help\(\):展示某数据集的帮助信息

直接输入数据集名称：获取数据集的内容

R中数据结构：标量、向量、矩阵、数组、数据框和列表。

R中数据类型\(模式\)：数值型\(\)、字符型、逻辑型、复数型和原生型（字节）。

R将实例标识符称为colnames，将类别型\(名义型和有序型\)变量称为因子。

对象：

在R中，对象（object）是指可以赋值给变量的任何事物，包括常量、数据结构、函数，甚至图形。对象都拥有某种模式，描述了此对象是如何存储的，即数据类型；以及某个类，像print这样的泛型函数表明如何处理此对象。

## 数据结构

class\(x\):查看数据结构

### 向量

向量是用于储存数值型、字符型和逻辑型数据的一维数组。

【单个向量的数据的数据类型一致】

c\(\)函数创建向量，\[\]索引， x\[x==10\]索引出向量x中等于10的值；

### 矩阵

矩阵是一个二维数组，和向量一样，每个元素都拥有相同的数据模式或者说类型。

matrix\(\)函数创建数组，\[ ,\]索引

### 数组

数组与矩阵类似，也只能存一种数据类型，但是维度可以大于2。

数组可通过array\(\)函数创建，\[ , , \]索引

### 数据框

R中最常处理的数据结构，像矩阵，但却是比较规则的列表，每一列数据类型一致。

函数data\.frame\(\)创建，$或\[\]进行索引

### 列表

是一些对象的有序集合，可以存储若干向量、矩阵、数据框，甚至其它列表的组合

函数list\(\)创建

### 因子（理解为因子型数据类型）

直接用字符变量也可以表示分类变量，但它只有字母顺序，不能规定想要的顺序，也不能表达有序分类变量。所以，有必要把字符型的分类变量转化为因子型，这更便于对其作后续描述、汇总、建模等（计算频数、独立性检验、相关性检验、方差分析、主成分分析、因子分析等）

类别变量\(名义型\)和有序类别变量\(有序型\)在R中称为因子。因子本质上是一个带有level属性的整数向量，其中“水平level”是指事前确定可能取值的有限集合，例如性别：男，女

因子在R中非常重要，因为它决定了数据的分析方式及如何进行视觉呈现。

函数factor\(\)创建

名义型：没有顺序之分的类别变量，表示类型

有序型：是一种顺序关系而非数量关系，比如:

status\(poor,improved,excellent\),仅表示比较，但不知道相差多少

连续型：呈现为某个范围内的任意值，并同时表示了顺序和数量，比如age

## 缺失值、字符、时间日期处理函数

__缺失值，存在这个值但不知道大小__

很多统计函数中添加na\.rm=TRUE可以忽略缺失值

is\.na\(x\):检测是否含缺失值

rowSums\(x\):显示x所含NA

na\.omit\(x\):去除x中所有的缺失值

na\.omit\(data\.frame\_name\):将数据框中所有包含缺失值的行删除

其它缺失数据：

NaN，代表不可能的值，例如0/0，不存在,可用is\.nan\(\)判断

Inf表示无穷，分为正无穷Inf和负无穷Inf，存在但不可能，例如1/0,\-1/0,可用is\.infinite\(\)判断

__字符串（stringr包）__

nchar\(x\):计算某字符串的长度，空格也包括；若x为数值型向量，则转换成统计相应元素数字个数

paste\(“”，“”，\.\.\.,sep=“”\):将字符串相连、压缩成一个字符串，没个字符串之间用sep间隔

paste\(x,””\)：x是字符型向量，表示将x中每一个字符串与“”中字符串相连

substr\(x=char\_vector\_name,start=a,stop=b\):提取字符型向量中每一个字符串从a到b个字符

chartr\(old, new, x\)：old是需要替换的字符，new是替换后的字符，x是被替换的字符向量；就是将x中出现的所有的old字符（识别到单个）换成new字符

toupper\(x\):将字符型向量中字符变成大写

tolower\(x\):转化为小写

gsub:此处使用到正则表达式，麻辣

grep:查看有没有匹配的元素

match\(\)

strsplit:字符串分割

outer\(obj1,obj2,FUN=paste,seq=””\):将两个对象进行组合，得到obj1\*obj2个对象，最后用paste进行连接,连接符为seq


__日期和时间__

时间序列分析：

包括对时间序列的描述、利用前面的结果进行预测

Sys\.Date\(\):获取现在时刻，class其为Date

as\.Date\(b<\- "2019\-01\-02"\)\#生成时间变量

as\.Date\(b,format = "%Y\-%m\-%d"\)\#指定年月日

?strftime:查看日期所有的表示格式

seq\(as\.Date\("2018\-09\-01"\),as\.Date\("2019\-1\-1"\),by=10\):生成时间序列，从第一个时间到第二个时间，间隔为10天

ts\(x,start=c\(2010,9\),end=c\(2019,9\),frequency = 1\)：生成时间序列，x是一个数值型向量，从第一个时间到第二个时间，间隔为1年，若frequency=4表示以季度变化，12表示月份

## 数据的输入

数据输入的方法包括从键盘输入、现有文本文件导入、从Excel电子表格、统计软件、数据库导入、在线导入

### 使用键盘输入数据

第一种、调用文本编辑器后手动输入数据

函数edit\(\)会调用一个允许手动输入的文本编辑器，具体包括：

1. 创建一个空数据框（或矩阵），其中变量名和变量的模式\(类型\)需与理想中的最终数据集一致

mydata <\- data\.frame\(age=numeric\(0\),gender=character\(0\)\)\#创建一个数据框，含有2个列（分别为数值型、字符型），并且都初始化为空；

（2）针对这个数据对象调用文本编辑器，输入你的数据，并将结果保存回此数据对象中。

mydata <\- edit\(mydata\)\#也可以写成fix\(mydata\)

第二种、直接在代码中嵌入数据集

mydatatxt<\-”xxxxxx”\#先写一个数据存成一个字符串，命名为mydatatxt；

mydata<\-read\.table\(header=TRUE,text=mydatatxt\)\#返回成数据框

### 从带分隔符的纯文本文件导入数据

使用read\.table（）函数从带分隔符的文本文件中导入数据。此函数可读入一个表格格式的文件并将其保存为一个数据框，压缩文件\.gz也能读。使用格式为：

mydataframe<\-read\.table\(“file\_name”,options\)\#file是一个带分隔符的ASCII文本文件；

options有：

header:第一行是否是变量名，header=TRUE表示可以将文件的第一行设置为文件名

sep:读取文件时进行分割的间隔符，sep=”\\t”为制表符；读入\.csv文件时应为sep=“,”（或使用read\.csv\(\)读取）。

comment\.char = '\!'：当读取文件时，以’\!'开头的行文本将被视为注释并被忽略。

colClasses=c\(“”,””\)：指定读取的数据类型

row\.names=””:指定标识符，被指定的列将没有标签

nrows=n：读取n行数据


file\(\),gzfile\(\),bzfile\(\),xzfile\(\),unz\(\),url\(\)可作为文件名参数使用，file\(\)可以访问文件、剪切板、C级别的标准输入；gzfile\(\),bzfile\(\),xzfile\(\),unz\(\)可以读取压缩文件

### 导入Excel数据

第一种 R自带操作

如果Excel文件在当前工作目录下，直接在右下的file窗口里左击该待导入文件——import dataset即可完成导入（出现报错有可能是rlang和vctrs等包的版本过旧）

也可以在environment窗口的第三个按钮进行导入（支持导入txt、Excel、SAS、SPSS等格式）

第二种 

在Excel中导出成\.csv文件，以\.csv文件导入到R

第三种

通过xlsx包直接导入Excel工作表（还需要xlsxjars包和rJava包，以及一个正常工作的Java安装）

read\.xlsx\(file,n\)\#file是Excel工作簿的所在路径，n是要导入的工作表序号

对于大型的工作簿，也可使用read\.xlsx2\(\)函数

第四种

也可以复制到clipboard再读取

### 访问数据库获取数据\(skip\)

R可以通过ODBC包连接任意一种连接到ODBC驱动的开放数据库

需要针对系统及数据库类型安装必要的驱动

### 读取网络文件（未完成）

将read\.table第一个参数换成网络文件具体位置

download\.file\(url,destfile,method,quiet=FALSE,mode="w",cacheOK=TRUE,extra=getOption\("download\.file\.extra"\)\) 

参数：

url:字符串,必须http://,https://,ftp://,file://开头 

destfile:下载文件的保存地址，默认工作目录，file为保存文件名 

method:提供"internal","wget","curl","lynx","libcurl","wininet"\.windows上通常internal就能解决大多数的问题，少数搞不定的如Cygwin,gnuwin32用"wget",windows二进制文件用"curl"\.method对于Mac用户来说是都要设置的\."lynx"主要针对historical interest 

quiet:TRUE\-禁止状态消息,显示进度条 

mode:写入文件模式,只能在method="internal"时使用,"w"/"wb"\(binary\)/"a"\(append\)/"ab" cacheOK:是否接受服务器端的缓存值,对http://,https://使用\.FALSE\-尝试从站点获取副本,而不是从中间缓存中获取

读取文件（skip）

读取一般的文件

help\(package=“foreign”）

读取剪切板：

read\.table\(“clipboard”,header=T,sep=”\\t”\):读取剪切板的内容，如果有变量名是clipboard的则需使用file=来完成；或者使用readclipboard\(\)直接读取

readLines\(“file\_name”,n=\)\#n为读入的最大行数

scan\(“file\_name”,what=”character”,nmax=n\):以字符串的格式读取前n个数据，还有nlines、skip参数等,也可以读入csv文件

scan\("tset\.txt", what = list\(first="",second="",studentSex=0,four=""\),skip=n\) \#以列表的形式读取数据,按列读取，第一列名称为first\.\.\.，其中第三列读入为数字，跳过第一行读；

scan还可以按照list中给的模板进行对有规律排列的数据进行分类：如

scan\("tset\.txt",what=list\(first\_name=character\(0\),second\_name=numeric\(0\)\)\)\#按照第一个是字符，第二个是数值把数据读完，所有偶数的都被读入第一列字符型（这就变成了先按行读再按列读）

当读入中文文本出现乱码时，采取将编码改为ANSI，原因：[为什么要修改编码](https://blog.csdn.net/Kyrie6c/article/details/115272901)

### 数据写出

write\(x,file=”x\.txt”,sep=”\.\.”\)

write\.table\(data,file\_name,sep=…\):将数据写入文本文件

write\.csv\(data, file\_name = "文件路径/文件名\.csv", row\.names = FALSE\):写入到新的csv文件；R每次读取文件会默认加一个行号，利用参数row\.names=FALSE来使之不加，还有col\.names=F表示不写入列名；

参数：

quote=FALSE:字符串不加双引号

append=TRUE:表示将该data添加到现有文件的末尾而不清除原数据；默认是FALSE，表示覆盖现有文件；

写成其它软件支持的格式可以使用foreign包，不开源就不行

### 文件压缩与解压

zip\(“file\_name”，“file\_name2”\):第一个参数输入压缩后的文件名，第二个参数输入压缩前的文件名，该函数未执行成功

unzip\(\)：需要解压的文件名

tar\(\)：第一个参数输入压缩后的文件名，第二个参数输入压缩前的文件名

untar\(\)：解压缩

write\.table\(x,gzfile\(“\.gz”\):将数据直接写成压缩文件

R 中默认没有解压相关文件的函数？？，需要使用一个包：R\.utils:

library\(R\.utils\)

gunzip\("file\.gz", remove = \`TRUE\`\):remove=TRUE只保留解压后的文件，原压缩包会被删除，默认就是TRUE

bunzip2\("file\.bz2", remove = \`TRUE\`\)

### 读取压缩文件

read\.table\(gzfile\(“\.gz”\):直接读取压缩文件，或直接利用read\.table\(“\.gz”\)

### 保存R文件

saveRDS\(\):存储为RDS文件，可用readRDS读取

save\(x1,x2,file=\):将两个对象都存进Rdata文件，Rdata文件可以保存对象（小心覆盖其他文件）

project文件可以保存图片

save\.image：保存空间中所有对象

### 实用的数据对象处理函数