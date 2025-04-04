# <font face="仿宋" color=orange>转录组下游分析</font>
# <center><font face="楷体" size=5>by 藏之地</font></center>
老师给的是tpm的表达矩阵（目前TPM才是基因定量单位的主流），关于TPM的定义暂时就先不管，直接往后走；
## 一、进入正式的差异分析前的准备知识（还是得简单了解一下TPM）
- [生信技能树-day19 转录组下游分析-标准化、聚类、差异分析](https://mp.weixin.qq.com/s/ghy2ecWGwL2Yc5-XDohulg)
### 1.1 数据标准化
1. 标准化前的数据预处理
    - 作用：过滤低表达的基因，并检查是否有异常样本;
    - 以下是常见的几种过滤方式（过滤的标准都可以自己调整）
        - 在至少在75%的样本中都表达的基因（表达是指在某个样本中count值＞0）；
        - 过滤平均值count<10的基因；
        - 过滤平均cpm<10的基因；
2. 标准化
    - 为什么要进行标准化？（疑惑：同一批次的样品，测序深度是一样的吗，或者说大概率是一样的吧？）
        - [我要自学生信之生信基础-转录组：分析流程大全解，看这一篇就够了](https://zhuanlan.zhihu.com/p/268518822)
        - 基因长度的影响：测序时，打断成的reads长度一般都是固定的（比如150bp）。如果基因A很长，基因B很短，那么基因A就可以打断成更多的片段，假如原本两基因表达量是一样的，但是由于基因A被打断成更多的片段，也就更容易比对到这个基因上；视基因类型而定，比如microRNA稳定在20+长度，则不用针对长度进行标准化。
        - 测序深度（sequencing depth）的影响（测序深度就是总数据量比上该物种基因组大小）：每个样本的测序深度（产生的数据量）不完全一样，同一条件下，测序深度越深，基因表达的read读数越多。
        - 所以标准化的主要目的就是去除这两个因素的影响。
    - 标准化的表达值与raw counts的使用场景：
        - 如果是差异表达分析，一般用的原始count值，比如limma、edgeR和DESeq2（暂时只用过DESeq2）（二代测序差异分析算法内部一般有自己的标化方法。）
        - 标准化后的值：基因表达值在样本与样本之间具有可比性。比如PCA分析，样本表达总体分布，生存分析，热图绘制，相关性分析等
3. [FPKM,TPM, Counts: 谁更适合做差异分析？](https://mp.weixin.qq.com/s/2nGsywVxvUg5X08-ZDQThQ)
    - 首先，gene counts不能与基因的表达量划等号很好理解，而TPM相比于FPKM考虑到了个样本文库大小之间的差异，更加准确；
    - "TPM, FPKM, or Normalized Counts?..."一文指出：TPM和FPKM仅适合用于样本内不同基因表达量的高低比较，而不能用于同一基因在不同样本、不同分组间的比较。具体成分上面这篇教程有讲到，总之就是建议使用基于counts的差异分析（即DESeq2或edgeR），而不要用TPM与FPKM这类经"normalization"后的值。
4. 几种标准化与log2FC
    - 教程
        - [RNAseq标准化丨CPM、RPKM、FPKM、TPM有什么区别与联系？（看表达式和例子）](https://mp.weixin.qq.com/s/9TOumsgc48w1RRpxrPMe_A) 
        - [RPKM、FPKM、TPM还在傻傻分不清？（有一个简单的计算过程）](https://mp.weixin.qq.com/s/yeqrOKxEBMeMh2Q-nuamsg) 
        - [数据标准化的几种方法，如何使用？代码如下（只用看代码）](https://mp.weixin.qq.com/s/kLx1sUAf_O-iK7-5hBqyKA)
        - [Counts FPKM RPKM TPM CPM 的转化](https://zhuanlan.zhihu.com/p/513391213)
    - CPM（只考虑了测序深度的标准化，没有考虑基因长度）：
        - CPM（Counts Per Million,每百万映射读数）是指将映射到转录本的原始读数数量，经过测序样本中的读数数量标准化后，乘以一百万。举个例子，某次RNA-seq中测序了一个包含500万个读数的文库。其中，总共有400万个读数（应该是一个样本里的）与基因组序列匹配，对于某个基因，有5000个计数在参考基因组上，则CPM为：
            - `CPM = 5000/(4*10^6)*10^6=1250`
        - CPM对RNA-seq数据进行了测序深度的标准化，但没有考虑基因长度。因此，尽管它是一种样本内标准化方法，但CPM标准化不适用于对基因表达进行样本内比较。
            ```
                cpm_normalize <- function(counts) {
                total_counts <- colSums(counts) # 计算样本的counts之和
                # print(total_counts)
                # print(t(counts))
                cpm <- t(t(counts) / total_counts) * 1e6
                return(cpm)
                }
            ``` 
    - FPKM/RPKM
        - FPKM（Fragments Per Kilobase Million，每百万片段的转录本千碱基数）适用于双端数据，而RPKM（Reads Per Kilobase Million，每百万读数的转录本千碱基数）适用于单端数据，它们校正了文库大小和基因长度的变化。经过上游处理，双端测序两个reads可以对应一个片段的过程已经完成，最后得到的counts就已经相当于是片段fragments了，因此下游分析由counts计算RPKM、 FPKM这两者的公式完全一致。
        - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250325102152.png"/>
            - 对于某个基因i，其映射到参考基因组上的计数为ri，而全部基因在参考基因组上的计数和为R；其中li是基因的长度（以千碱基为单位），10^3是用于基因长度的标准化的因子，而10^6则是用于测序深度的标准化因子。
        - 举个例子，某次RNA-seq中测序了一个包含500万个读数的文库。其中，总共有400万个读数与基因组序列匹配，对于某个基因，其长度为2000，有5000个计数在参考基因组上，则RPKM为：
            - `RPKM=5000*10^3*10^6/(4*10^6*2000)=625`
        - 注意，以上标准化方法只适用与样本内的比较，不适合用于样本间的比较。原因在于，对于不同的样本，总计数R是不相同的。
            ```
                counts2FPKM <- function(count=count, efflength=efflen){ 
                    PMSC_counts <- sum(count)/1e6   #counts的每百万缩放因子 (“per million” scaling factor) 深度标准化
                    FPM <- count/PMSC_counts        #每百万reads/Fragments (Reads/Fragments Per Million) 长度标准化
                    FPM/(efflength/1000)            #FPKM                      
                    } 
            ``` 
    - TPM
        - 由于每个样本的总标准化计数都会不同，所以又提出了TPM。TPM的计算方法同RPKM很类似，同样的对基因长度和测序深度进行标准化，只不过RPKM是先进行测序深度标准化，后进行基因长度标准化；而TPM是「先进行基因长度标准化，后进行测序深度标准化」。事实证明，TPM的标准化方法更有优势。
        - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250325104132.png"/>
        - 将每个转录本的相应RPKM和TPM值进行加总后，可以发现不同转录本的总RPKM并不相同，而进行TPM变换后的加总TPM值是相同的。事实上所有进行TPM变换后的转录本的加总TPM值都是相同的（正常情况下，是百万）。由于RNA-seq就是为了通过比较不同样本间的标准化后的Read数差异来得出基因表达量差异的结果，那么不同样本的加总RPKM不同，就会导致无法通过直接比较RPKM值确定两者的差异。
            ```
                # 假设你已经有了转录本长度信息
                gene_length <- c(10000, 20000, 15000)
                # 手动实现TPM标准化
                tpm_normalize <- function(counts, gene_length) {
                rpk <- counts / (gene_length / 1000)
                print(rpk)
                rpk_sum <- colSums(rpk)
                tpm <- t(t(rpk) / rpk_sum) * 1e6
                return(tpm)
                } 
            ``` 
    - log2FC
        - 一般在做基因表达分析时，会对数据进行log2转化，目的是避免极大差异值的过度偏倚，让数据更符合正态分布，便于后续分析。假设：在A组中，基因X的表达量为x在B组中，基因X的表达量为y，则`log2FC = log2(x+1)-log2(y+1) = log2((x+1)/(y+1))`  
### 1.2 异常样本与重复性检测
1. [转录组专题|关于样本重复性问题](https://zhuanlan.zhihu.com/p/444061613)
    - [如何区分生物学重复和技术性重复？](https://zhuanlan.zhihu.com/p/276402875)
        - 技术性重复：对**单个个体**的相同的实验，比如对一个人进行三次采血或者对一个人的采血样品进行三次测量都属于技术性重复；因为只测量单个个体，所以只能拿到该个体样本的精确测量结果，但是不能把结果类推到群体中。
        - 生物学重复：使用不同个体进行测量数据，可以使结论具有普适性。
        - 对一般实验来说，增加生物学重复，减少技术重复会更划算。比如，做转录组测序时，最好做生物学重复，而不是技术重复。
    - 每个处理多少个生物学重复？
        - ① 对于动植物样品，建议3~5个生物学重复，对生物学样品之间做相关性检验，提高实验结果的可信度；
        - ② 对于细胞样品，生物学重复之间的差异性相对较小，建议3个以上生物学重复；
        - ③ 对于临床样品，由于供试者的基因型、生活方式、生活环境、年龄、性别可能存在较大差异，可能需要更多的生物学重复，一般10个生物学重复以上。
        - 在转录组测序时，一般不建议设置两个重复。因为如果两个重复样品结果不一致，无法确定以哪个数据为参考。
    - 判断组内重复性好坏的方法：
        - 在实际分析中，通常结合PCA和相关性系数综合判断样本是否离群。
            - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250317210956.png"/> 
        1. PCA：
            - 原理：用方差（Variance）来衡量数据的差异性，将高维数据用某几个综合指标来表示。将原本鉴定到的所有基因的表达量重新线性组合，形成一组新的综合变量，同时根据所分析的问题从中选取2-3个综合变量，使它们尽可能多地反映原有变量的信息，从而达到降维的目的。
            - 关于转录组测序，我们可能获得上万个基因的表达信息，那么利用PCA可将样本所包含的上万个维度的信息（上万个基因的表达量），降维至某些维度的综合指标（主成分）表示。一般选取PC1和PC2，来解释样本间的重复性好坏与组间样本的差异度。如下图PCA散点图，组内样本呈现相互聚集，说明组内的重复性比较好。
            - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250317211251.png"/>
            - 在文章中，也会看到三维的PCA图。这时选取了PC1，PC2，PC3去解释样本间的距离。PC1+PC2（+PC3）越大，对方差解释度越大，越具有说服力。
        2. 相关系数：
            - 通常计算两个样品之间的Pearson或Spearman相关系数判断组内重复性情况。相关系数越接近1，样品间相似度越高。一般情况下，组内生物学样本相关系数大于组间样本，则表明组内重复性较好；
            - 一般情况下，计算相关性系数时，对于生物学重复（如采样时个体差异）之间的相关系数依据经验建议在0.7以上较好；对于技术重复（实验操作、实验仪器等）之间的相关系数依据项目经验来说在0.85以上比较合理。
            - 对于转录组数据，可以利用样本的表达谱来计算样本间的相关性，通过计算相关系数r来评估每组样本的生物重复性。最常用的度量是Pearson和Spearman相关系数。对于Pearson相关系数很简单，主要用来衡量两个数据集的线性相关程度。
            - 置于如何选择：看下面例子：比如，某个实验做了3次生物学重复，那有理由假设这3次重复线性相关。而如果是一个基因和另一个受到调控的基因的表达水平，或者某个基因顺式作用元件的染色质开放程度，和这个基因表达水平之间的关系就可能需要假设单调相关。
            - 与PCA的比较：更能判断样本的重复性，因为PCA为把对样品贡献大的信息保留，所描述的是整体所有组的特征；而相关系数直接呈现的是两组样品之间的相关程度。若相关系数越高，表明两组样品之间的相关程度越高，即重复性越好。
        3. 样本聚类：可用于判断在不同实验条件下的表达模式。依据样品的表达谱进行聚类，样品之间重复性较好时通常会聚在同一分支下。如果组内样本重复性较差可能会呈现无规则的聚类形式；
        4. 重复性散点图：展示组内样本的重复性情况。图中偏离对角线的点越少，样品间的相关性越高，重复性越好。
    - 样本离群了还能用于分析吗？
        - 首先判断离群程度，若离群程度较小，则可以尝试设置阈值，缩小基因范围，再次重新进行相关性分析判断样本是否离群。若离群程度很大，对后续差异分析的结果造成了很大的影响，那么可以考虑将该样本剔除，再进行后续差异分析等等。转录组测序通常要求设置3个生物学重复样本，如果样本足够多，建议比预期实验设计多送1~2个样本测序，以便后续某个样品与组内其它样本出现离群情况，直接剔除离群样本，省时省力。若测序样本较少，无法剔除样本，也可以考虑对同一批次的备份样本再次测序，后续再重新分析。
2. 进行异常样本与重复性检测的目的
    - 检查组内样本的重复性是否好;
    - 检查组间样本是否可以较好的分开;
    - 是否有离群样本;
### 1.3 差异表达分析  
## 二、进行异常样本与重复性检测
[有了TPM，怎么做基因表达分析、相关性分析和主成分分析](https://www.jianshu.com/p/d1bb8f6cd46b)
### 2.1 样本表达总体分布图
1. 目的
    - 使用箱线图或小提琴图或概率密度分布图去观察：1、单个样本的数据分布特点；2、多个样本之间的差异。
2. 步骤：
### 2.2 PCA分析
1. 理论知识
    - 教程：
        - [【官方双语】一个视频带你理解PCA（主成分分析）！（推荐）](https://www.bilibili.com/video/BV1dqikY5EBi/?spm_id_from=333.337.search-card.all.click&vd_source=2523c7055f0985a7f47ca59739b6b086)
        - [PCA原理和简单推导](https://www.bilibili.com/video/BV1X54y1R7g7/?spm_id_from=333.337.search-card.all.click&vd_source=2523c7055f0985a7f47ca59739b6b086)
        - [未看](https://zhuanlan.zhihu.com/p/37777074)
    - 相关背景
        - 对于含有多个变量的大数据进行分析时，如果分别对每个指标进行分析，分析往往是孤立的；而盲目地减少指标又会损失很多有用的信息，从而产生错误的结论。因此希望有一种方法可以在减少需要分析的指标的同时，尽量减少原指标包含信息的损失，以达到对所收集数据进行全面分析的目的。由于各变量之间存在一定的相关关系，因此可以考虑将关系紧密的变量变成尽可能少的新变量，使这些新变量是两两不相关的，那么就可以用较少的综合指标分别代表存在于各个变量中的各类信息。主成分分析与因子分析就属于这类降维算法。
    - 基本原理
        - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/efbe4b9f70298b5229281abb1811390.jpg"/>  
2. 实际操作
    - [R语言的ggplot2+ggforce包绘制散点图并添加分组边界](https://mp.weixin.qq.com/s/EbFvY0okvaJZqAHI0u4eDQ)
    - [转录组专题11| 如何得到这么漂亮的PCA图？](https://mp.weixin.qq.com/s/ZzLUoYMzDXRrQbnCS59iBA)
3. 可视化（都未整理）
    - [【R>>ggforce】给Cluster画个小圈圈](https://www.jianshu.com/p/93d6d98a799a)
    - [R语言的ggplot2+ggforce包绘制散点图并添加分组边界](https://www.jianshu.com/p/50e75cb66651)
    - [错误:点太少，无法计算具有3个点的椭圆？-R](https://cloud.tencent.com/developer/ask/sof/105654411)
### 2.3 样本相关性分析
1. 理论知识
    - 教程：
        - [数据分析-相关性分析-理论](https://www.bilibili.com/video/BV1Zg411L7Vd/?spm_id_from=333.337.search-card.all.click&vd_source=2523c7055f0985a7f47ca59739b6b086)
        - [临床医生科研作图 —— 样本间相关性分析](https://www.bilibili.com/video/BV1EG411q7xU/?spm_id_from=333.337.search-card.all.click&vd_source=2523c7055f0985a7f47ca59739b6b086)
    - 度量变量之间的关系有哪些？
        - 函数关系：一一对应；
        - 相关关系：
            - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250323104505.png"/>
            - 完全正线性相关不等于函数关系，因为样本的完全正线性相关不一定等于总体的完全正线性相关，需要进行检验。
        - 因果关系：比较难。
    - 什么时候需要进行样本相关性分析？
        - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250323111305.png"/>
        - 基因间的相关性就是两个基因在这些样本表达值的相关程度，样本间的相关性就是两个样本的这些基因的表达值是否相关。
        - 可视化形式：散点图：一个图只能展现两个样本之间的相关性、而热图可以在一个图中展现每两个样本间的相关系数；
    - **样本**相关性程度的度量（线性相关）
        - 皮尔逊相关系数
            - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250323105132.png"/>
            - 使用该相关系数的条件
                - 样本数据来源于正态总体
            - 相关系数的显著性检验
                - t检验
            - 注意事项
                - 非线性相关性也会导致线性相关系数很大，所以使用该参数时先画个散点图看看大致关系；
                - 离群点对相关系数的影响也会很大，需要提前去除，也可画个散点图看看；
                - 相关系数为0，只能说不是线性相关；
        - 斯皮尔曼相关系数 
2. 实际操作
### 基于TPM值的筛选差异基因
1. 理论部分
    - 教程
        - [数据是TPM，想做差异只能转换？（Wilcoxon秩和检验）](https://mp.weixin.qq.com/s/Z68LhqHlcBNrK6QhiXFy9A)
        - [只有fpkm怎么进行基因表达差异分析？](https://www.zhihu.com/question/445830860) [只有tpm怎么进行基因表达差异分析？](https://www.zhihu.com/question/1059639456) （都建议试试limma-trend）
        - [TPM to differential expression(建议获取raw counts)](https://www.biostars.org/p/9470568/)
        - [Differential expression analysis starting from TPM data（不建议用TPM做差异表达基因筛选）](https://support.bioconductor.org/p/98820/)
        - [What can I do if I only have TPM but not raw counts data?（建议实在不行试试limma-trend）](https://support.bioconductor.org/p/123155/)
    - 背景
        - 常用的差异分析方法，如limma、edgeR、DESeq2进行差异分析都需要用到counts，而后续很多分析都是基于log2(TPM+1)，想要补一个差异分析，需要另外收集counts数或者对TPM值进行转换，这两种可能都比较麻烦，有一些方法可以直接利用TPM进行差异表达基因筛选，比如（1）采用tximportR包，将结果导入到DESeq2种进行分析；（2）直接采用sleuthR包进行差异分析；（3）利用Wilcoxon秩和检验+FDR矫正；（4）使用limma-trend包；。
        - 方法一和二都是要在指定的定量软件下定量比较好，不然没用。
2. 利用Wilcoxon秩和检验+FDR矫正（[最好有前置内容：edgeR和limma包使用](https://www.jianshu.com/p/bdf2b72b8761)）
    1. 准备TPM数据与所用的包
        - egdeR、limma、future.apply包
    2. 数据前期处理并标准化
        - 使用EdgeR的filterByExpr()删除低表达基因；
        - TMM (Trimmed Mean of M-values) 标准化，使样本间表达量更可比。
    3. 对每个基因进行Wilcoxon秩和检验
        - 用你future.apply进行加速
    4. 计算每个基因的差异倍数（logFC）
    5. 基于FDR阈值的输出结果
3. 利用sleuth包进行差异分析（和下面这个方法一样对我没用）
    - 教程
        - [使用salmon和sleuth进行小麦RNA-seq差异表达分析](https://www.jianshu.com/p/73420cb70548)
        - [RNA-seq:Kallisto+Sleuth (2)](https://www.jianshu.com/p/460722ecd23e)
        - [sleuth:基于TPM值的差异分析](https://mp.weixin.qq.com/s/5p6yrB1JFOWZ9mSIoIxPXg)
    - 安装与简介
        - 它根据Kallisto获得的定量结果进行差异分析；此外，它支持在复杂实验设计背景下进行统计检验，并通过sleuth live提供交互式探索性数据分析；
        - `BiocManager::install("pachterlab/sleuth")`，首先要有devtools。
    - 使用方法（使用salmon和Kallisto进行定量都可以）
        1. 选择使用wasabi包进行结果转换
            - sleuth包是为Kallisto量身定制的，如果是salmon软件进行定量，可以通过wasabi包进行转换；
        2. 准备分组文件sample_infor.txt和转录本ID与基因ID对应的文件transcript_gene_relation.txt
            - 分组信息文件包括sample和condition两列；注意第一列的顺序要按字母顺序排列；
            - ID对应文件为transcript_id和gene_id两列；
        3. 读取salmon的定量数据
            - salmon的结果文件好像不止一个； 
        4. 读取样本分组信息文件和ID对应关系文件
        5. 设置两两比较的实验组
            - 比如`a <- list(c("CS_CT","ABA1h"), c("CS_CT","ABA12h"), c("CS_CT","ABA24h"),c("ABA1h","ABA12h"), c("ABA1h","ABA24h"), c("ABA12h","ABA24h"))`
        6. 进行差异表达分析（DEA）
            1. 构建sleuth对象
            2. 进行统计检验
            3. 获取显著性结果
            4. 保存差异表达结果
            5. 获取标准化表达矩阵
4. 利用tximport包+DESeq2进行差异分析（来源可以是Kallisto或者Salmon的定量结果，反正我这不知道哪来的是不行的，而且应该是一系列结果文件）
    - 教程：
        - [通过tximport将Salmon结果传递给DESeq2最直白过程](https://www.jianshu.com/p/c3efde425f0b)
        - [Stringtie+tximport+DESeq2 (tximport具体操作向）](https://zhuanlan.zhihu.com/p/85332471)
        - [官方文档](https://bioconductor.org/packages/release/bioc/manuals/tximport/man/tximport.pdf)
        - [tximport 将 Salmon 定量结果导入 DESeq2（挺详细的）](https://mp.weixin.qq.com/s/ZqYCNZzKX7LdXh13h26MnQ)
    - 安装与简介：
        - 使用BiocManager安装；
        - 用于简化TPM值定量等文件导入到DESeq2，以进行下游分析；
    - 使用方法：
        1. 读取转录本与基因关系文件transgene.csv：
            - 该文件的第一列为转录本id，第二列为基因id，需要加表头；
        2. 获取或构建sample的DataFrame:
            - 第一列为run（如SRR号），第二列为condition（control or treat）；
        3. 读取基因表达文件：
            - `txi <- tximport(files, type="salmon", tx2gene=tx2gene)`
                - type可以是"salmon", "sailfish", "alevin", "piscem", "oarfish", "kallisto", "rsem", "stringtie",or "none" 
        4. 将tximport导入的结果传递给DESeq2：
            - `dds <- DESeqDataSetFromTximport(txi, colData=samples, design= ~ condition)` 
5. 学习limma包
    - 教程
        - [官方文档](https://bioconductor.org/packages/release/bioc/vignettes/limma/inst/doc/usersguide.pdf)
### 2.4 定量
由于老师后面给了比对数据，所以不用再用TPM进行差异基因筛选。
1. 软链接源文件以及MSU的基因组注释文件
    - 首先由于bam文件较大，可以将Data目录下的upload文件夹通过软链接链接到当前目录：`ln -s /gss1/home/huangju02/hj/Data/RiceRNA/upload ./01_bam_files`。
    - `ln -s /gss1/home/huangju02/hj/vvv/rice3k/NIP_genome_data/nip_version7/all.gff3 ` 放在`/gss1/home/huangju02/hj/vvv/RNA_seq`下面。
2. 确定这个bam文件是否经过排序
    - 命令如下：`samtools view ./1-1-1Aligned.toTranscriptome.out.bam | less -SN`
    - 这个bam文件是根据name进行排序的，但是有几点问题：它的第三列是转录本ID而不是染色体名称；而且它的MPOS和ISIZE列都是0，但是CIGAR/比对模式都是150M，都能够match上。
    - 只要是经过排序，不管是根据fragment name还是根据pos；
3. 鉴定数据的建库方式（失败）
    1. 准备bed文件
        - 由于MSU没有提供对应的gtf文件，所以只能看看Ensembl的GTF文件可不可行。同样将其下载到`ln -s /gss1/home/huangju02/hj/vvv/rice3k/NIP_genome_data/ensembl_data/`之后再用软链接到`/gss1/home/huangju02/hj/vvv/RNA_seq`下面。
        - 执行`gtfToGenePred ../Oryza_sativa.IRGSP-1.0.60.chr.gtf Oryza_sativa.IRGSP-1.0.60.chr.genePred`以及`genePredToBed Oryza_sativa.IRGSP-1.0.60.chr.genePred Oryza_sativa.IRGSP-1.0.60.chr.bed`
    2. 使用infer_experiment.py程序
        - `infer_experiment.py -i ../01_bam_files/1-1-1Aligned.toTranscriptome.out.bam -r all.bed -q 20` 
        - 最后果然还是不行。建议尝试使用gffread将gff转换成gtf，[something like gffread my.gff3 -T -o my.gtf](https://www.biostars.org/p/234181/)。结果是：使用了gffread转换后的gtf文件依旧没用，报同样的报错：`Unknown Data type`，猜测可能是前期比对的过程与当前所用的文件不一致。
4. 对新发的rawbam鉴定建库方式
    1. 查看bam文件确定其染色体名称为chr01，而获取到的gtf文件的染色体名称为Chr1，`sed 's/^Chr\([0-9]\)\b/chr0\1/' all.gtf > processed.gtf`；
    2. 准备bed文件：`gtfToGenePred ./processed.gtf rseqc/processed.genePred` 和 `genePredToBed rseqc/processed.genePred rseqc/processed.bed`
    3. 使用infer_experiment.py程序：`cd rseqc/`和`infer_experiment.py -i ../02_rawbam/1-1-1.rg.markdup.split.bam -r processed.bed -q 20`：结果为非特异性。
        - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250401113432.png"/>
        - 警告信息暂时忽略。
5. 利用htseq进行表达定量
    1. 先改造gff文件的染色体名称：`sed 's/^Chr\([0-9]\)\b/chr0\1/' all.gff3 > processed_nip7.gff3`；
    2. 新的bam文件的排序方式，是根据pos排序的；
    3. 编写脚本执行htseq
6. 根据htseq的结果count.txt生成可以用于DESeq2的表达矩阵文件
7. 利用DESeq2进行差异基因筛选；
### 三种方法的差异基因集     
将根据Wilcoxon检验、直接计算log2FC以及用DESeq2计算的差异基因集绘制韦恩图。


