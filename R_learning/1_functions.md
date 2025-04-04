# <font face="仿宋" color=orange>杂七杂八</font>
# <center><font face="楷体" size=5>by 藏之地</font></center>
包括一些常用的函数、快捷键、报错信息的处理。
## 一、函数集合
### 1.1 R功能函数（R包、help、R工作空间等）
包含对R包操作、帮助函数、软件设置方面的操作
#### 1.1.1 R包函数
1. 包外操作（不涉及包的使用）：
    ```
    R.version：查看当前R的版本
    library():查看当前库里的包
    .libPaths()：查看库（library）位置（包括用户的个人库和R的系统库，已经过迁移到D盘）
        第一个路径"C:/Users/mingweiwang/AppData/Local/R/win-library/4.3"是当前用户的个人库文件夹路径，其中包括用户安装的R包。
        第二个路径"C:/Program Files/R/R-4.3.2/library"是R的系统库文件夹路径，其中包括系统安装的R包。  
    install.packages("vcd") #下载vcd包，默认是最新的
    install.packages(c("vcd","caer")#下载两个或多个包
    library(name):加载名为name的包；或require()
    installed.packages():列出已安装的包的相关信息
    packageVersion("package_name")：查看某个包的版本信息
    search():查看当前环境中哪些包已被加载
    detach("package:vcd")：从当前环境下移除已加载的包
    update.packages():更新安装的包
    remove.packages("vcd"):删除已下载vcd包
    ```
2. 包内操作：
    ```
    help(package="vcd")：查看vcd包的帮助文档(包括包的函数和数据集)，或使用library(help="vcd")
    ls("package:vcd")：查看已加载包所含函数
    data(package="vcd")：查看已加载包的数据集
    ```
#### 1.1.2 帮助函数
1. 针对packages：
    ```
    help.start():打开帮助文档首页，进入浏览器
    help(package=ggplot2)：查看包的帮助文档，""可加可不加
    vignette(""):可以查看更多的关于包的内容（即显示vignette文档，vignette翻译为简介）
    vignette():显示已安装包中所有可用的vignette文档
    ```
2. 针对function：
    ```
    help()或者?接函数名:查看函数帮助文档(常用)
    ??接函数名:当未加载包时直接看函数的帮助文档
    args():查看函数所用的参数而不需打开帮助文档
    example():查看函数的实例，绘图函数还会给出图
    ```
3. 更多搜索：
    ```
    help.search(""):在本地进行模糊搜索一个函数，比如搜索绘制热图heatmap的函数；也可以直接使用??heatmap进行本地搜索
    RSiteSearch("")：通过网络进行搜索某关键字(包括在线文档和R-Help邮件列表的讨论文章)
    apropos("sum"):列出所有包含sum字段的内容
    apropos("sum",mod="function")：搜索含sum字段且是函数的内容（或用mode）
    ```
#### 1.1.3 R工作空间、history函数
1. 工作目录相关：
    ```
        getwd():显示当前的工作目录
        setwd("mydirectory"):修改当前的工作目录为mydirectory，但不会直接创建新的；如果想用相对目录，可以像Linux一样使用./new_directory来表示当前目录下的某个目录；
        list.files():显示当前工作目录的文件
        file.remove('myfile')：删除当前目录下的myfile文件
    ```
2. 处理当前工作空间中的对象：
    ```
        ls():列出当前工作空间(环境)中的对象或变量
        ls.str():显示环境中变量及其信息
        rm(obj):移除（删除）一个或多个对象
        rm(list=ls())：清空当前环境中所有的变量，此处的list是一个option参数，这是非常常用的一个操作；
    ```
3. 保存工作空间和命令历史：
    ```
        history(#):显示最近使用过的命令（默认值为显示25个）
        savehistory（"myfile"）:保存命令历史到文件myfile中（默认值为.Rhistory）
        loadhistory（"myfile"）：载入一个命令历史文件（默认值为.Rhistory）
        save.image("myfile.RData")：保存工作空间的环境中的变量和函数到文件myfile中（默认值为.RData），但不会保存图片；
        save(obj,file="myfile.RData"):保存指定对象到一个文件中
        load("myfile.RData"):读取一个工作空间(.RData文件)到当前会话中（默认值为.RData）
        q():退出R，将会询问你是否保存工作空间
    ```
#### 1.1.4 R文件输入
source("filename"):执行包含在该工作目录下指定文件中的R语句集合
#### 1.1.5 结果输出与保存
sink("filename"):将以下代码的输出结果保存到指定文件中，默认情况下，若文件已经存在，则它的内容将被覆盖；参数
sink()：仅输出到控制台
以下是一些图片的保存:
    ```
    bmp（"filename.bmp"）:图片以BMP格式输出到指定文件
    jpeg（"filename.jpg"):JPEG文件
    pdf（"filename.pdf"）:PDF文件
    png（"filename.png"）:PNG文件
    postscript（"filename.ps"）:PostScript文件
    svg（"filename.svg"）:SVG文件
    win.metafile（"filename.wmf"）:Windows图元文件
    dev.off():将输出返回到终端（保存图片之后使用）
    dev.new()
    ```
### 1.2 缺失值、字符、时间日期处理函数
1. 缺失值，存在这个值但不知道大小
    ```
    很多统计函数中添加na.rm=TRUE可以忽略缺失值
    is.na(x):检测是否含缺失值
    rowSums(x):显示x所含NA
    na.omit(x):去除x中所有的缺失值
    na.omit(data.frame_name):将数据框中所有包含缺失值的行删除
    ```
    其它缺失数据：
    ```
    NaN，代表不可能的值，例如0/0，不存在,可用is.nan()判断
    Inf表示无穷，分为正无穷Inf和负无穷Inf，存在但不可能，例如1/0,-1/0,可用is.infinite()判断
    ```
2. 字符串（stringr包）
    ```
    nchar(x):计算某字符串的长度，空格也包括；若x为数值型向量，则转换成统计相应元素数字个数
    paste(""，""，...,sep=""):将字符串相连、压缩成一个字符串，没个字符串之间用sep间隔
    paste(x,"")：x是字符型向量，表示将x中每一个字符串与""中字符串相连
    substr(x=char_vector_name,start=a,stop=b):提取字符型向量中每一个字符串从a到b个字符
    chartr(old, new, x)：old是需要替换的字符，new是替换后的字符，x是被替换的字符向量；就是将x中出现的所有的old字符（识别到单个）换成new字符
    toupper(x):将字符型向量中字符变成大写
    tolower(x):转化为小写
    gsub:此处使用到正则表达式
    grep:查看有没有匹配的元素
    match()
    strsplit:字符串分割
    outer(obj1,obj2,FUN=paste,seq=""):将两个对象进行组合，得到obj1\*obj2个对象，最后用paste进行连接,连接符为seq
    ```
3. 日期和时间
    - 时间序列分析：包括对时间序列的描述、利用前面的结果进行预测
        ```
        Sys.Date():获取现在时刻，class其为Date
        as.Date(b<- "2019-01-02")#生成时间变量
        as.Date(b,format = "%Y-%m-%d")#指定年月日
        ?strftime:查看日期所有的表示格式
        seq(as.Date("2018-09-01"),as.Date("2019-1-1"),by=10):生成时间序列，从第一个时间到第二个时间，间隔为10天
        ts(x,start=c(2010,9),end=c(2019,9),frequency = 1)：生成时间序列，x是一个数值型向量，从第一个时间到第二个时间，间隔为1年，若frequency=4表示以季度变化，12表示月份
        ```
### 1.2 R统计函数（临时放这）
- 包含一些数据中的统计量以及统计模型
    ```
    统计量相关：
    mean():均值
    sd():标准差
    cor():求数据之间的相关度
    table():进行频数统计，或生成列联表

    模型及算法：
    lm():拟合回归模型，如lmfit <- lm(mpg~wt,data=mtcars)，对mtcars数据集中的mpg和wt两个做拟合
    summary(lmfit):分析结果的统计概要
    cooks.diatance():计算和保存影响度量统计量，此处选择Cook距离作为度量影响的统计量
    predict():预测
    ```
## 二、 快捷键
仅展示常用快捷键，更多可参考[快捷键](http://blog.sina.com.cn/s/blog_403aa80a0101ar8q.html)
1. 控制台区（console）
    - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250326155509.png"/>
2. 工作区（代码区）
    - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250326155552.png"/>
    - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250326155618.png"/>
## 三、R报错与警告
### 3.1 报错
1. `non-numeric argument to binary operator`
    - 该报错显示出现即表达矩阵里面含有非数值型变量的character(字符)
    - 解决方法一：把数据类型转化成numeric型，代码例子如下:
        - mydata1[,c(3:7)] <- as.numeric(unlist(mydata1[,c(3:7)]))
    - 解决方法二：删除(某列)非numeric数据
2. `Error in make.names(col.names, unique = TRUE):invalid multibyte string 1`
    - 解决方法：.csv中有中文，可以加一个encoding = "UTF-8"，如`bio<-read.csv("bio_1.csv",encoding = "UTF-8")`
### 3.2 警告 
1. `warning message：程辑包’xxxx’是用R版本4.1.3来建造的`
    - 不是报错，它建议你进行更新，不然会有问题，一般忽略它就行。
