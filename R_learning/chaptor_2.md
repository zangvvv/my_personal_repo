# <font face="仿宋" color=orange>创建数据集</font>
# <center><font face="楷体" size=5>by 藏之地</font></center>
包括R中的数据结构、数据的输入、数据的导出等
## 一、数据集、数据结构、对象的概念
1. 数据集通常是矩形数组，行表示观测，列表示变量。
    ```
    data():列出已加载包中所含所有可用实例数据集
    help():展示某数据集的帮助信息
    直接输入数据集名称：获取数据集的内容
    ```
2. R中数据结构：标量、向量、矩阵、数组、数据框和列表。
R中数据类型(模式)：数值型()、字符型、逻辑型、复数型和原生型（字节）。
R将实例标识符称为colnames，将类别型(名义型和有序型)变量称为因子。
3. 对象：
在R中，对象（object）是指可以赋值给变量的任何事物，包括常量、数据结构、函数，甚至图形。对象都拥有某种模式，描述了此对象是如何存储的，即数据类型；以及某个类，像print这样的泛型函数表明如何处理此对象。
## 二、数据结构
class(x):查看数据结构
head():访问数据的前几行，默认为5吧
tail()
str(object):查看对象信息
### 2.1 向量
1. 向量的特性
    - 向量是用于储存数值型、字符型和逻辑型数据的一维数组。
    ```
    class(c(7,"j")) # 【单个向量的数据的数据类型一致，即向量是同质数据结构，而列表是异质数据结构】如果不一致，会被隐式地修改为一致
    class(c("n","j"))#字符型向量返回的数据类型是character
    c()函数创建向量，[]索引， x[x==10]索引出向量x中等于10的值；
    向量的命名：names函数，x是一个向量，给它命名：names(x)<-c("a","b","ab")，取消命名就是设置为NULL；
    length(x):查看一个向量的长度
    ```
2. 相关函数
    - 创建
        ```
        rep(x,times=n,each=m):先指定需要重复生成的值或向量，再设定x整体要重复的次数和循环的次数。即x中每个数字先重复m次再循环n次
        runif（n,min=a,max=b）:得到均匀分布的随机样本，即生成在a-b之间的n个随机数，runif意为random samples from uniform distribution
        seq(a,b,length.out=n):将a到b分成n份，R中没有1:2:5这种输入方式
        append(x=x,value=n,after=m):在向量a中第m个数值后追加一个值为n的数值
        intersect(x,y)：对x和y两个向量取子集；
        ```
    - 运算
        ```
        x**n(或y),乘幂运算，等于x^n
        x%%n(或y)取余运算，x%/%n(或y)整除运算
        abs():计算绝对值
        sqrt():计算平方根
        log(x,base=a):求以a为底的的对数，无base默认为自然对数；log10(x)可直接用
        exp():计算指数
        ceiling(x):返回不小于x的最小整数
        floor(x):返回不大于x的最小整数
        trunc(x):返回x的整数部分
        round(x,digits=n):对x四舍五入,保留位数为n位小数
        signif(x,digits=n):四舍五入，保留n位有效数字
        sum(x):求和
        max(x)：最大值，min最小值
        range（x）：返回最大值和最小值
        mean（x）：均值
        var（x）：方差
        sd（x）：标准差
        prod（x）：连乘的积
        median（x）：中位数
        quantile（x，probs=c(0.3,0.5,0.9)）：计算30%、50%、90%百分位数，默认四分位
        which()：返回值所在位置，如which.max(x):返回最大值的位置索引值，也可以用min；which（x==n）返回x中值为n的位置；which(x>n)返回大于n的位置
        ```
### 2.2 矩阵
1. 矩阵的特性
    - 矩阵是一个二维数组，和向量一样，每个元素都拥有相同的数据模式或者说类型。
    - `matrix()函数创建数组，[ ,]进行索引`
2. 相关函数
    - 创建
        ``` 
        matrix(x,nrow=numbers_of_rows,ncol=n_o_c,byrow=T,dimnames=list(char_vector_rownames,char_vector_colnames)):其中x包含矩阵元素；nrow和ncol指定行和列数；byrow=T表示按行填充，默认按列；char_vector_colnames为字符型向量，其中的也可以在后面补加:
        dimnames（matrix_name） <- list(c("b","c"),c("s","d","c"))列名行名
        ```
    - 矩阵索引
        ``` 
            x[n,]表示返回矩阵x中第n行全部数值
            x[n,c()]:返回矩阵x中第n行的c（）数值
            x[-1,n]:返回第n列中除第一个数值
            也可以通过名称索引
            dim(x)<-c(m,n):添加维度变成矩阵
        ```
    - 矩阵运算：
        ```
        colSums(x):计算矩阵x每一列的和,有NA则会计算成NA
        rowSums(x):
        colMeans(x):
        x*x：对应元素相乘，称为内积
        x %*% x:计算外积，
        diag(x):返回对角线数值
        t(x)：进行转置
        ```
### 2.3 数组
1. 数组的特性
    - 数组与矩阵类似，也只能存一种数据类型，但是维度可以大于2。
    - 数组可通过array()函数创建，[ , , ]索引
### 2.4 数据框
1. 数据框的特性
    - R中最常处理的数据结构，像矩阵，但却是比较规则的列表，每一列数据类型一致。
    - 函数data.frame()创建，$或[]进行索引
2. 相关函数
    - 创建
        `data.frame(col1,col2,col3,...,stringsAsFactors = FALSE):列向量可以使任何类型，每一列的名称可由函数name指定。`
    - 数据框索引：
        - [提取数据框元素](https://www.jianshu.com/p/365bf1bd4481)
            ``` 
                同样可以用[] or [c()]
                data_frame_name["name"]:返回数据框中的name这一列，为一个数据框
                data.frame_name[,"name"]:返回数据框中name这一列，为一个vector
                data.frame_name$name:类似前者，常用
                attach(data.frame_name):将数据框添加到搜索路径，表示遇到一个变量名后，将检查路径中的数据框，检查该变量是否已经存在于某个数据框，就不需要$来索引了；但当原环境已经有了一个相同名称的原变量，原变量将取得优先权；此时可采用with()函数
                【应尽量避免使用attach；变量名较短、重复次数较少时直接使用$;或直接说明数据来源，即添加参数data=data_name;使用with()或与之类似的within()】
            ``` 
            例子：
                ```
                    attach(data.frame_name)
                    plot(mpg,disp) # 以mtcars为例，在红色句子中就不用mtcars$mpg或mtcars$disp
                    detach(data.frame_name)#将数据框从搜索路径中删除
                ```
            例子：
            ```
            with(mtcars,{
            print(summary(mpg))
            plot(mpg,disp)
            })
            ```
            对{}内的每条语句都是对mtcars执行，如果仅有一条语句，{}可省略，但是with()内的赋值仅在此函数内生效，无法在global_env中找到此中赋值的对象。此时可以将赋值符号换成<<-,它可以将对象保存到with()之外的global_env中
            使用[]和[[]]来取出其中的数据分别以数据框或vector显示，前者比喻成从火车中取出的一节车厢加车头，可以运行（为数据框），后者只是车厢，不能运行
    - 实例标识符：
        可通过数据框操作函数中的rowname参数选项指定，用于区分数据集中不同的个体，如病例数据中的病人编号：             
        data,frame(patientID,age,diabetes,row.names=patientID)#将patientID指定为R中标记各类打印输出和图形中实例名称所用的变量
    rownames():查看所有行名
    colnames():查看所有列名
### 2.5 列表
1. 列表的特性
    - 是一些对象的有序集合，可以存储若干向量、矩阵、数据框，甚至其它列表的组合
    - 函数list()创建
2. 相关函数
    ```
        dim(x)<-c(m,n,o):给x添加维度变成数组
        array(x,numeric_vector_dimension,char_list_dimnames)#x包含数组中数据；dimension给出各维度最大值；
        list(name1=object1,name2=object2,object3,...)：name可以省略，object指任意结构

        列表访问：
        list_name[n]:访问第一个列（元素）
        list_name[c()]:访问c（）的列（元素）
        list_name[c(“name1”,”name2”)]：通过名称访问
        list_name$name:访问name这个元素
        list_name[[n]]:与list_name[n]不一样，输出的是第n个元素本身的数据结构，而后者数据结构是list；也可以吧n换成”name”
        list_name[[n]]<-x:给list中第n个元素修改成x；删除就是利用负索引或NULL
    ```
### 2.6 因子（理解为因子型数据类型）
1. 因子的特性
    - 直接用字符变量也可以表示分类变量，但它只有字母顺序，不能规定想要的顺序，也不能表达有序分类变量。所以，有必要把字符型的分类变量转化为因子型，这更便于对其作后续描述、汇总、建模等（计算频数、独立性检验、相关性检验、方差分析、主成分分析、因子分析等）
    - 类别变量(名义型)和有序类别变量(有序型)在R中称为因子。因子本质上是一个带有level属性的整数向量，其中“水平level”是指事前确定可能取值的有限集合，例如性别：男，女。因子在R中非常重要，因为它决定了数据的分析方式及如何进行视觉呈现。
    - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250326171409.png"/>
    - 函数factor()创建
        名义型：没有顺序之分的类别变量，表示类型
        有序型：是一种顺序关系而非数量关系，比如:status(poor,improved,excellent),仅表示比较，但不知道相差多少
        连续型：呈现为某个范围内的任意值，并同时表示了顺序和数量，比如age
2. 相关函数
```
    函数factor()以一个整数向量的形式存储类别值，整数的取值范围是[1...k](k是名义型变量中唯一值的个数)，同时一个由字符串(原始值)组成的内部向量将映射到这些整数上：
    plot(mtcars$cyl)：显示为散点图
    plot(factor(mtcars$cyl))：显示为柱状图
    普通因子：
    factor(x):将向量x存储为名义型变量的因子，具体赋值根据字母顺序而定
    有序因子：
    factor(x,ordered=TRUE):将向量x存储为有序型变量的因子。
    如果x是字符型vector，各水平排序顺序根据字母顺序而定；或者通过指定levels来覆盖默认排序：
    factor(x,ordered=TRUE,levels=c(“”,””)):各水平排序顺序则是修改为c(“”,“”)中所指定的顺序；若x有，但未在参数中列举的元素将被设置为缺失值。
    如果x是数值型量：
    factor(x,levels=c( , ),labels=c(“”,””)):标签的顺序和水平一致，levels中的数值是数值型变量x中有的值，同样x有但不在levels中的将被设为缺失值；labels中的字符串将代替原数值输出
    cut(x,c(seq(a,b,n)))：将向量x按照从a-b，每间隔n作一个区间对x进行分类
```
## 三、数据的输入
数据输入的方法包括从键盘输入（edit函数）、直接在代码中嵌入数据集、现有文本文件导入、从Excel电子表格、统计软件、数据库导入、在线导入。
### 3.1 从带分隔符的纯文本文件导入数据
1. 使用read.table（）函数从带分隔符的文本文件中导入数据。此函数可读入一个表格格式的文件并将其保存为一个数据框，压缩文件.gz也能读。将read.table第一个参数换成网络文件具体位置就可以读取在线数据。还可以读取剪切板。
    - 使用格式为：`mydataframe<-read.table(“file_name”,options)`；
    - options有：
        header:第一行是否是变量名，header=TRUE表示可以将文件的第一行设置为文件名
        sep:读取文件时进行分割的间隔符，sep=”\\t”为制表符；读入.csv文件时应为sep=“,”（或使用read.csv()读取）。
        comment.char = '\!'：当读取文件时，以’\!'开头的行文本将被视为注释并被忽略。
        colClasses=c(“”,””)：指定读取的数据类型
        row.names=””:指定标识符，被指定的列将没有标签
        nrows=n：读取n行数据
    - read.table(gzfile(“.gz”)):直接读取压缩文件，或直接利用read.table(“.gz”) 
### 3.2 导入Excel数据
1. 第一种 R自带操作
如果Excel文件在当前工作目录下，直接在右下的file窗口里左击该待导入文件——import dataset即可完成导入（出现报错有可能是rlang和vctrs等包的版本过旧）
也可以在environment窗口的第三个按钮进行导入（支持导入txt、Excel、SAS、SPSS等格式）
2. 第三种 通过xlsx包直接导入Excel工作表（还需要xlsxjars包和rJava包，以及一个正常工作的Java安装）
    - read.xlsx(file,n)#file是Excel工作簿的所在路径，n是要导入的工作表序号。对于大型的工作簿，也可使用read.xlsx2()函数
    - 当读入中文文本出现乱码时，采取将编码改为ANSI，原因：[为什么要修改编码](https://blog.csdn.net/Kyrie6c/article/details/115272901)
### 3.3 数据写出
1. 相关函数
        ``` 
        write(x,file=”x.txt”,sep=”..”)
        write.table(data,file_name,sep=…):将数据写入文本文件
        write.csv(data, file_name = "文件路径/文件名.csv", row.names = FALSE):写入到新的csv文件；R每次读取文件会默认加一个行号，利用参数row.names=FALSE来使之不加，还有col.names=F表示不写入列名；
        ```
    - 参数：
        - quote=FALSE:字符串不加双引号
        - append=TRUE:表示将该data添加到现有文件的末尾而不清除原数据；默认是FALSE，表示覆盖现有文件；
        - 写成其它软件支持的格式可以使用foreign包，不开源就不行
### 3.4 文件压缩与解压
1. 相关函数
        ```
            zip(“file_name”,“file_name2”):第一个参数输入压缩后的文件名，第二个参数输入压缩前的文件名，该函数未执行成功
            unzip()：需要解压的文件名
            tar()：第一个参数输入压缩后的文件名，第二个参数输入压缩前的文件名
            untar()：解压缩
            write.table(x,gzfile(“.gz”):将数据直接写成压缩文件
        ```
    - R中默认没有解压相关文件的函数？？，需要使用一个包：R.utils:
        ``` 
        library(R.utils)
        gunzip("file.gz", remove = `TRUE`):remove=TRUE只保留解压后的文件，原压缩包会被删除，默认就是TRUE
        bunzip2("file.bz2", remove = `TRUE`)
        ```
### 3.5 保存R文件
        ```
        saveRDS():存储为RDS文件，可用readRDS读取
        save(x1,x2,file=):将两个对象都存进Rdata文件，Rdata文件可以保存对象（小心覆盖其他文件）
        project文件可以保存图片
        save.image：保存空间中所有对象
        ```