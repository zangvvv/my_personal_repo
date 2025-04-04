# <font face="仿宋" color=orange>htseq_deseq2_pipeline</font>
#  <center><font face="楷体" size=5>by 藏之地</font></center>
## 第一步 使用HTSeq进行表达定量
- 教程
    - [从NCBI下载数据后的处理7——HTseq比对，定量，生成count.txt文件-2023-08](https://mp.weixin.qq.com/s/F1hiYj5X1hStuWEpQdlj7A)
    - [RNA-seq第四期——HTSeq-count对reads进行计数（包含结果的合并）-2022-09](https://mp.weixin.qq.com/s/BFmtOfu2lX1GCqlWscfLxw)
    - [RNA-seq(6): reads计数，合并矩阵并进行注释（也包含结果的合并）-2018-08](https://www.jianshu.com/p/6d4cba26bb60)
    - [转录组数据分析—HTseq定量(也包含合并，不过它为什么用limma包)--2022-10](https://www.jianshu.com/p/682916b324a6)
    - [【转录组】RNA-seq分析htseq-count的使用(包含另外几个软件提取count_matrix的使用)--2018-07](https://www.cnblogs.com/triple-y/p/9338890.html)
    - [转录组分析（二）Hisat2和DESeq2差异分析(未看)-2022-09](https://zhuanlan.zhihu.com/p/560995288)
    - [RNA-Seq-workflow(未看)](https://sites.google.com/site/princetonhtseq/tutorials/rna-seq)
- 安装与简介
    - HTSeq是一款针对有参转录组测序数据进行表达量分析的python包，需要有SAM和GTF文件作为输入文件；输入的gtf不能包含可变剪切信息，并且与sam的染色体名称保持一致；接受的bam或sam文件需要经过排序，可以用`samtools sort`进行排序；
    - HTSeq需要python3.7以上的环境，以及不支持windows，因为其中的一个dependency——Pysam只在linux或osx有；使用`conda install bioconda::htseq -y`安装成功；
- 使用方法：
    - 主要命令：`htseq-count -f bam -r name -s no -a 10 -t exon -i gene_id -m intersection-nonempty {yourfile_name.bam} {gtf|gff file(如果选择的feature一样则结果是差不多的)} > counts.txt`
        - -f {bam|sam}：指定输入文件的格式，默认是sam；
        - -r {name|pos}：设置所输入的sam或bam文件的排序方式，可以选择按read名或者pos按照参考基因组位置排序；双端测序数据必须指定，单端可忽略；默认按照read名。（好多教程句子前后都是矛盾的，而且教程对于参数都是抄来抄去，但是pypi的官方文档真的写的很垃圾，建议还是看其它英文教程或帮助文档）
        - -s {yes,no或reverse}：设置是否是链特异性测序，默认是yes；
        - -m {union|intersection-strict|intersection-nonempty}：对于原核生物，推荐使用intersection-strict模式；对于真核生物，推荐使用union模式。
        - -i：设置feature ID是由gtf/gff文件第9列哪个标签决定的；
        - -n：线程数，[htseq也能支持使用-n参数来指定线程了](https://evvail.com/2020/11/19/1950.html)
    - 鉴定数据的建库方式（链特异性或链非特异性）
        - 教程：
            - [技能——如何判断测序数据是否是链特异性——2022-01](https://www.jianshu.com/p/109997345a67) 
            - [47--填坑—鉴定数据的建库方式（链特异性或非链特异性）【原创】](https://www.sxmu.edu.cn/bdcd/info/1109/1373.htm)
            - [RSeQC判断链特异性（strand-specific）](https://www.jianshu.com/p/4987dce4d165)
            - 后面两个不太重要：
                - [RSeQC的安装及操作流程](https://zhuanlan.zhihu.com/p/662280955)
                - [RSeQC使用笔记](https://www.bioinfo-scrounger.com/archives/234/)
        - 方法一：文献的method部分或数据下载界面看是否有提示
        - 方法二：使用igv
            - 比对完成后，将 bam 文件在 igv 上进行可视化，以单端测序为例，如果你发现测序的read方向总是与所在基因的方向一致或者总是相反，那么就是链特异性测序；反之，则不是。 
        - 方法三：使用RSeQC的infer_experiment.py工具
            1. 下载rseqc：
                - 直接使用conda安装成功，安装成功后实际上是多了一些脚本，而不是下载了这个软件，例如可以使用`infer_experiment.py -h`查看使用方法；
            2. 准备bed12文件
                - 使用rseqc的时候需要将注释文件gtf转换为bed12文件（有12列内容），可以使用UCSC 的 gtfToGenePred 和 genePredToBed 工具进行转换；
                - 教程：[RSeQC的安装及操作流程](https://zhuanlan.zhihu.com/p/662280955) [技能——gtf转为bed12](https://www.jianshu.com/p/de2455a8f507)
                1. 下载MH63RS2的gtf文件： https://ftp.ensemblgenomes.ebi.ac.uk/pub/plants/release-60/gtf/oryza_sativa_mh63/
                2. 下载UCSC 的 gtfToGenePred 和 genePredToBed 工具，都可以直接conda下载成功；
                3. 使用（非常简单）
                    ```
                    mkdir 11_rseqc && cd $_
                    gtfToGenePred ../Oryza_sativa_mh63.MH63RS2.60.gtf Oryza_sativa_mh63.MH63RS2.60.genePred
                    genePredToBed Oryza_sativa_mh63.MH63RS2.60.genePred Oryza_sativa_mh63.MH63RS2.60.bed
                    ```
            3. 使用infer_experiment.py程序（提供的gtf好像需要与比对所用的一致）
                - 主要命令：`infer_experiment.py -i ../4_mapping_tophat/result_SRR10751892/accepted_hits.bam -r Oryza_sativa_mh63.MH63RS2.60.bed -q 20`
                    - -i 比对生成的bam文件（可以不用排序）
                    - -r gtf转bed12文件产生的bed文件。
                    - -s 从所有的reads中抽取多少进行统计（默认200k）
                    - -q unique map的mapq阈值
            4. 结果解读
                - 如果两个数值接近1:1，则为非链特异性，如果比例悬殊则为链特异性；“1+-，1-+，2++，2--”这，也就是read1在+链，相对的gene其实是在-链（reverse）。这种就是“fr-firststrand”相比于fr-secondstrand现在更为常见。
                - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250315220011.png"/>
## 第二步 根据htseq的结果count.txt生成可以用于DESeq2的表达矩阵文件
- 教程：
    - [RNA-seq(6): reads计数，合并矩阵并进行注释](https://www.jianshu.com/p/6d4cba26bb60)
    - [RNA-seq第四期——HTSeq-count对reads进行计数](https://mp.weixin.qq.com/s/BFmtOfu2lX1GCqlWscfLxw)
- 示例
    ```
    ## 导入包
    library(biomaRt)
    library(stringr)
    ## 合并count矩阵
    setwd("D:/AAA_codefile/stata_of_R/RNA-seq/")
    mh63_yl0<-read.table("data/SRR10751892_counts.txt",sep = "\t",col.names = c("gene_id","mh63_yl0"))
    mh63_yl1<-read.table("data/SRR10751893_counts.txt",sep = "\t",col.names = c("gene_id","mh63_yl1"))
    mh63_pc0<-read.table("data/SRR10751894_counts.txt",sep = "\t",col.names = c("gene_id","mh63_pc0"))
    mh63_pc1<-read.table("data/SRR10751895_counts.txt",sep = "\t",col.names = c("gene_id","mh63_pc1"))
    tail(mh63_yl0)
    raw_count <- merge(merge(mh63_yl0, mh63_yl1, by="gene_id"), merge(mh63_pc0, mh63_pc1, by="gene_id"))
    raw_count_filt <- raw_count[-1:-5,] # 删除最后五行
    ```
## 第三步 使用DESeq2筛选差异表达基因
- 教程
 - [一文掌握R包DESeq2的差异基因分析过程（还挺详细的）-2021-01](https://www.jianshu.com/p/b5541d695108) 
 - [R语言DESeq2包做转录组RNAseq差异表达分析的一个简单小例子（视频）](https://www.bilibili.com/video/BV1HU4y157Cq/?spm_id_from=333.337.search-card.all.click&vd_source=2523c7055f0985a7f47ca59739b6b086)
 - [Analyzing RNA-seq data with DESeq2（Bioconductor官方文档）-2025-02](https://bioconductor.org/packages/devel/bioc/vignettes/DESeq2/inst/doc/DESeq2.html)
 - [RNA-seq workflow: gene-level exploratory analysis and differential expression--作为上面的一个补充(没看)](https://master.bioconductor.org/packages/release/workflows/vignettes/rnaseqGene/inst/doc/rnaseqGene.html)
 - [转录组分析 | DESeq2 差异表达分析（基本就是对官方文档的演绎）-2024-07](https://mp.weixin.qq.com/s/cXqG1T8PcK4VT0sx7dcRdA)
 - [转录组分析（三）：DESeq2差异表达(未看)](https://mp.weixin.qq.com/s/fGZZuSPkBc2ZbUimHNimtQ)
- 使用DESeq2 R包进行筛选差异基因
       - 简介与安装
           - 转录组测序的最直接目的，就是设法寻找组间显著表达变化的基因，解释基因表达水平的变化对生物功能的影响。作为目前使用频率最高的鉴定差异分子的R包之一，一般可以在文献中得到它的阈值（log2(fold change)>=2、p<0.05）
           - 如果下载好了BiocManager，直接输入`BiocManager::install('DESeq2')`即可安装；也可以用devtools从github上安装最新版本；
       - 使用方法：
           1. input data and pre-process
               1. characteristics of input data
                   - DESeq2的输入文件是所有测序样本的基因表达矩阵的表格文件；该表格文件的列名为样本id或组别，行名为特征id（比如gene_id）、表格的值就是reads count；该表格文件的值均为整数值，不能是标准化的counts，因为DESeq2有自己的标准化流程；表格文件示例如下：
                       - 图片 
                   - 表达矩阵需要用DESeqDataSet对象进行存储
                       - 使用read.delim函数读取表达矩阵，然后用data.frame转成dataframe类型（也可以用read.csv）；然后使用`DESeqDataSetFromMatrix`函数构建DESeqDataSet对象；
               2. pre-filtering
                   - 去除掉一些表达特别低的基因/行；降低缓存压力，参考：设置为10；
                       ```
                       smallestGroupSize <- 3
                       keep <- rowSums(counts(dds) >= 10) >= smallestGroupSize
                       dds <- dds[keep,]
                       ``` 
               3. note on factor levels
                   - 也就是给数据添加组别标签，可以用factor或relevel函数；也可以在后面使用results函数的时候指定；
               4. Collapsing technical replicates
                   - DESeq2还提供一个函数去除技术性的重复片段；
           2. 进行差异表达分析
               1. 标准的步骤被整合成一个DESeq()函数，然后使用results函数获得包含各基因的`log2 fold changes, p values and adjusted p values`的表格，用于后续的差异基因筛选；
               2. 筛选差异表达基因
                   - 根据阈值（比如log2FC≥1以及padj<0.01）筛选，并通过“up”和“down”分别区分上、下调的基因。 
               3. 其它可用的
                   - 加速和并行策略：使用DESeq函数可能因为样本过大导致运行缓慢，可以通过BiocParallel包使用多线程进行执行；
                   - 有一个IHW包用于对DESeq2的p值进行检验；官方文档还提到什么效应量收缩；
           3. 结果可视化以及文件输出
               1. 使用内置的绘图函数进行可视化和输出
                   - 内置一个plotMA函数达到类似火山图的效果（针对log2 fold change）
                       - 图片 
                   - lfcShrink函数可以对log2 fold change进一步处理，使其更加方便排序和可视化；
                   - 有一个plotCounts函数可以针对特定基因进行normalizes counts值的绘制；
                       - 图片
                   - 提供了一些包，能够对DESeq的结果进行输出成pdf、csv或html格式、以及完成plot的输出；  
               2. 使用ggplot2绘制火山图；
