# <font face="仿宋" color=orange>linux和Rmarkdown</font>
# <center><font face="楷体"
## 一、R in linux
### 1.1 linux安装R和R包
1. 安装R
    - conda search r：查看当前R的版本；
    - conda install r=版本号 -n env_name：将指定版本的R下载到指定的虚拟环境中；（当时最新的是r_channel的4.3.1版本，r-base会有4.3.2，后者内存占用更小，前者大概1G）
2. 安装R包
    - [用install.packages()来安装R包，但是后面不太好管理，比如迁移conda环境时，用install.packages()来安装的R包会缺少；](https://cloud.tencent.com/developer/ask/sof/1442785)
    - conda install r-包名，如r-ggplot2；一些Bioconductor包的名字为bioconductor-前缀，如bioconductor-ballgown
## 二、Rmarkdown
[b站庄闪闪-Rmarkdown教程(网盘有pdf教程)](https://www.bilibili.com/video/BV1ib4y1X7r9/?p=10&spm_id_from=333.880.my_history.page.click)
[Rmarkdown第一弹-CSDN博客-庄闪闪](https://blog.csdn.net/qq_37379316/article/details/114418432?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522171361437916800213067363%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=171361437916800213067363&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-1-114418432-null-null.nonecase&utm_term=Rmarkdown&spm=1018.2226.3001.4450)
[第4章 与其他语言的结合 | R Markdown 指南 (bookdown.org)](https://bookdown.org/qiushi/rmarkdown-guide/other-languages.html)
[7.4 可滚动代码块 （*） |R Markdown 食谱 (bookdown.org)](https://bookdown.org/yihui/rmarkdown-cookbook/html-scroll.html)
[R Markdown 指南 (cosname.github.io)](https://cosname.github.io/rmarkdown-guide/)
### 2.1 简介与特点
1. 优点/特性
    - Rmarkdown是R语言中的Markdown编辑工具，它相比于单纯的Markdown笔记，还可以在其中插入可以运行的代码块，并将代码运行结果输出到Markdown中，支持的语言不限于R、python（需要安装reticulate包并进行配置）、bash等；输出格式可以是 HTML、docx、pdf、beamer 等。
2. 缺点/比较烦人的地方
    - Rmarkdown的开发貌似已停止更新维护（开发Rmarkdown为代表的文档沟通生态的谢益辉已离职），其渲染成中文pdf报错不断（深有体会），并且随文档体积增大，渲染速度变慢甚至失败。
3. 建议
    - 尽量用于写比较短的代码流程，不要强求输出文档的美观（本人为此花了很多功夫却成效不高）；如果输出成中文pdf比较困难，直接用Rmarkdown代码或者html就将就看吧。
### 2.2 Rmarkdown 进阶操作
Rmarkdown的语法来源于markdown，很多语法都是一样的，故在此仅介绍Rmarkdown的特性。
1. 如何换行？
    - [MarkDown中如何换行？_markdown换行-CSDN博客](https://blog.csdn.net/u014163312/article/details/117934339)
    1. 方法一：行末写一个\后enter或者按2个空格后再enter；（统一用后者）
    2. 方法二：段落换行就直接在中间空一整行，这样做同时有换行和空一行的作用；
2. 代码块使用
    1. 插入代码块
        - 行内插入代码：用英文状态下的\`，然后写一个r，即表示用R语法来书写该\`\`内的代码（貌似只支持r）：`r sin(pi/2)`
        - 插入代码块（最常用）：使用快捷键Ctrl+Alt+I生成R代码块，可以将R改为Python就会变成Python代码块：
            - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250401221023.png"/>
            - 默认情况下代码和结果都会在输出⽂件中呈现；代码块的第一个参数为代码块名称，暂时就发现可以用于标识代码块（一个文件中代码块不能重名）；
    2. [可以通过在{r }中设置参数来控制代码块运⾏结果的输出情况](https://yihui.org/knitr/options/)
        - 代码块中的一些选项参数：
            - eval、include和echo选项：
                - eval=FALSE显示代码不显示结果：eval控制是否将内容作为代码去执行，默认是TRUE；这样的代码段如果有标签，可以在后续代码段中被引⽤（不是很清楚这个代码引用）。
                    - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250401221712.png"/> 
                - include=FALSE运行代码，但不显示代码也不显示结果：
                    - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250401221733.png"/> 
                - echo=FALSE显示输出结果但不显示代码块内容：echo参数控制了markdown是否显⽰代码块。
                    - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250401221908.png"/>
                - 以上三个选项的效果也可以在代码块右侧更改：
                    - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250401222101.png"/>
            - collapse、prompt和comment选项：
                - ⼀个代码块的代码、输出默认被分解为多个文本块中，collapse=TRUE会将多个结果放在一起：
                    - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250401222420.png"/>
                    - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250401222448.png"/>
                - prompt=TRUE 会使代码⽤R的⼤于号提⽰符开始；如果希望结果不⽤井号注释结果，可以使⽤选项comment=''指定成无（或者其它文本）：
                    - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250401222642.png"/>
            - results选项（暂略）
        - 全局设置：
            - 全局代码块通过knitr::opts_chunk$set函数进⾏设置，⼀般设置在YAML⽂件下⽅，具体见下图：
                - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250401222925.png"/> 
            - 如果想要单独修改某个代码块和全局设置不一样，也是可以的：
                - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250401223011.png"/> 
3. 从模板创建Rmarkdown笔记
    - 教程
        - [13.1 Get started | R Markdown: The Definitive Guide (bookdown.org)](https://bookdown.org/yihui/rmarkdown/rticles-start.html)
        - [Rmarkdown第四弹—— 主题格式更换_修改rmarkdown模版-CSDN博客](https://blog.csdn.net/qq_37379316/article/details/115734718?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522171361437916800213067363%2522%252C%2522scm%2522%253A%252220140713.130102334.pc%255Fblog.%2522%257D&request_id=171361437916800213067363&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~blog~first_rank_ecpm_v1~rank_v31_ecpm-3-115734718-null-null.nonecase&utm_term=Rmarkdown&spm=1018.2226.3001.4450)
    1. 下载rticles包或者其它几个[Rmarkdown模板相关的包](https://blog.csdn.net/qq_37379316/article/details/115734718)
        - 下载rticles包需要首先安装Rtools：
            1. 使用下面的代码会自动帮你下载最新的Rtools：
                ```
                    install.packages("devtools")
                    devtools::install_github("gadenbuie/rsthemes") 
                ```
            2. 如果上述方法不行，可以参考进行尝试:
                - [ Rtools下载 Rtools下载与安装(win10) - 知乎](https://blog.csdn.net/liukaiyue99/article/details/131470413#:~:text=%E5%AE%89%E8%A3%85Rtools%201%20%E5%9C%A8%E4%B8%8A%E8%BF%B0%E5%AE%89%E8%A3%85R%E7%9A%84%E7%AC%AC4%E6%AD%A5%E7%9A%84%E9%A1%B5%E9%9D%A2%EF%BC%8C%E9%80%89%E6%8B%A9%20Rtools%202%20%E9%80%89%E6%8B%A9%E7%9B%B8%E5%BA%94%E7%89%88%E6%9C%AC%E7%9A%84Rtools%20%E7%94%B1%E4%BA%8E%E6%88%91%E4%BB%AC%E5%89%8D%E9%9D%A2%E5%AE%89%E8%A3%85%E7%9A%84%E6%98%AFR%204.3.1%EF%BC%8C%E6%89%80%E4%BB%A5%E6%88%91%E4%BB%AC%E5%BA%94%E9%80%89%E6%8B%A9%E5%AE%89%E8%A3%85Rtools,%EF%BC%883%EF%BC%89%E7%82%B9%E5%87%BB%20Next%20%EF%BC%884%EF%BC%89%E7%82%B9%E5%87%BB%20Install%20%EF%BC%885%EF%BC%89%E7%AD%89%E5%BE%85%E5%AE%89%E8%A3%85%E5%AE%8C%E6%88%90%20%E7%82%B9%E5%87%BB%20Finsh%20%E5%88%B0%E6%AD%A4Rtools%E7%9A%84%E5%AE%89%E8%A3%85%E5%B7%B2%E5%AE%8C%E6%88%90) 
        - 使用`install.packages("rticles")`或者`devtools::install_github("rstudio/rticles")`下载rticles包；后者可能更新比较及时，不过我用的是前一种；
        - 本人下了一个rmdformats包，主要是一些html文件的模板，还没看效果；
    2. 在global options里的Sweave勾选用tinytex生成tex文件（tinytex输出pdf是先生成tex再转换为pdf文件）
        - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250401224407.png"/>
    3. 从模板创建Rmarkdown：`from Template`，如果是输出中文pdf则选择`CTeX Documents`模板；
    4. 修改yaml表头
        - 创建后的文件暂时只能看到knit to tex，修改yaml表头的output内容后再保存或knit一下就可以看到其它几个选项，而且之前不可以knit成功word文档也可以knit了：
            - output内容修改为：
            ```
                output:
                  pdf_document:
                    latex_engine: xelatex
                    fig_caption: true
                    number_sections: true
                    toc: true
                  rticles::ctex:
                    fig_caption: yes
                    number_sections: yes
                    toc: yes
                  word_document:
                    toc: true
                  html_document:
                    toc: true
                    df_print: paged
            ```
    5. 然后就可以`Knit to pdf`，之前报错，今天又直接成功了，看来文件还是得小点； 
4. Rmarkdown输出成pdf
    - 教程：
        - [Rmarkdown导出中文PDF解决方案 - 知乎](https://zhuanlan.zhihu.com/p/96993644#:~:text=%E6%80%BB%E7%9A%84%E6%9D%A5%E8%AF%B4%EF%BC%8C%E9%80%9A%E8%BF%87Rmarkdown%E8%BE%93%E5%87%BA%E4%B8%AD%E6%96%87%E7%89%88pdf%E6%8A%A5%E5%91%8A%EF%BC%8C%E8%A6%81%E8%A7%A3%E5%86%B3%E4%BB%A5%E4%B8%8B%E4%B8%A4%E4%B8%AA%E6%A0%B8%E5%BF%83%E9%97%AE%E9%A2%98%EF%BC%9A%20%E5%AF%BC%E5%87%BAPDF%E9%9C%80%E8%A6%81%E9%85%8D%E7%BD%AETeX%E7%8E%AF%E5%A2%83%EF%BC%8C%E4%BB%A5%E4%B8%8B%E4%B8%A4%E7%A7%8D%E6%96%B9%E6%A1%88%E9%83%BD%E5%8F%AF%E4%BB%A5%EF%BC%9A%20%E5%AE%89%E8%A3%85TinyTex%20%E5%AE%89%E8%A3%85%E4%BB%BB%E4%B8%80LaTeX%E5%A5%97%E4%BB%B6%EF%BC%88TeX%20LiveCTeXMiKTexMacTeX%EF%BC%89%202.,PDF%E4%B8%AD%E6%96%87%E6%98%BE%E7%A4%BA%E9%97%AE%E9%A2%98%EF%BC%8C%E4%B8%8B%E9%9D%A2%E4%B8%A4%E7%A7%8D%E6%96%B9%E6%A1%88%E4%B8%AD%E6%9B%B4%E6%8E%A8%E8%8D%90%E5%90%8E%E8%80%85%EF%BC%9A%20%E5%9C%A8YAML%E4%B8%AD%E5%86%99%E5%85%A5LaTeX%E7%9A%84%E4%B8%80%E4%BA%9B%E8%AE%BE%E7%BD%AE%EF%BC%9B%20%E4%BD%BF%E7%94%A8%20rticles%20%E5%8C%85%E4%B8%AD%E7%9A%84CTeX%20Documents%E6%A8%A1%E6%9D%BF)
    1. 安装配置Tex环境
        - 方法一：安装TinyTeX包，[TinyTeX 中文文档](https://yihui.org/tinytex/cn/)
            - 直接在Rstudio控制台输入以下命令：`install.packages('tinytex')`和`tinytex::install_tinytex()`，貌似需要Rtools。
            - 一台电脑上同时存在多种TeX环境会产生冲突，因此益辉大神建议安装TinyTex之前需要卸载电脑中的其他 LaTeX 套装（TeX Live 或 MiKTeX 或 MacTeX）。
            - 我之前应该是安装过textlive；
        - 方法二：安装texlive
            1. 下载安装最新的[texlive](https://www.tug.org/texlive/)
                - 详细步骤：download on DVD——downloading the ISO image and burning your own DVD——download from a nearby CTAN mirror——下载大概4个G的iso文件——解压后安装到D盘
                - 检查是否安装成功：搜索栏搜索Texworks，然后输入一下代码，保存后（英文路径和文件名）——选择XeLaTex，点击左边的小绿色三角按钮运行，如果能输出中文则表示安装成功。
                    ```
                        \documentclass[UTF8]{ctexart}
                        \begin{document}
                        \section{中文} 中文论文排版测试，还不错。
                        \end{document}
                    ``` 
                - 可能遇到的报错：
                    - [texlive安装过程中报错 vars expected but powershell](https://blog.csdn.net/qq_50698753/article/details/130475564)
    2. 然后就可以从模板创建Rmarkdown笔记了
        - 文件头参数：见实际脚本中的内容，还有注意如果中文不显示，可能需要一句：`CJKmainfont: Microsoft YaHei`:
            - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250402134439.png"/>
    3. 小贴士和报错处理：
        - 有时pdf输出时报错，也可能有pdf生成；且pdf渲染出的效果比html好看；
        - python代码快的输出不要太长，否则也会渲染失败，还有就是，过长它只会被截断而不会换行；
            - 待补充

### 2.3 报错大赏（后续再整理）
1. 输出成pdf时报错`! xdvipdfmx:fatal: Unable to open "xxx.pdf No output PDF file written. ! xelatex.exe: fwrite: Broken pipe"`：
    - 可能是因为对应的pdf正在使用，关闭对应的pdf文件，重新渲染即可；
2. 如果在Rmarkdown中运行代码时，需要读取文件，报错`Error: no more error handlers available (recursive errors?); invoking 'abort' restart`，可能是Rmarkdown的原因，可以重新运行一次，或者复制代码到.R文件去再运行；
3. 报错`Error: no more error handlers available (recursive errors?); invoking 'abort' restart 警告: 'type2char'里的29类型还没有被实现错误于scan(file = file, what = what, sep = sep, quote = quote, dec = dec, : INTEGER() can only be applied to a 'integer', not a 'unknown type #29'`
    - [建议更新一下Rstudio（对我确实有用）](https://stackoverflow.com/questions/78494337/r-debugonce-error-error-no-more-error-handlers-available-recursive-errors)
            