# <font face="仿宋" color=orange>RNA-seq原理与分析流程</font>
#  <center><font face="楷体" size=5>by 藏之地</font></center>
## 一、RNA-seq背景介绍
1. RNA-seq
   - 生物研究中，经常需要知道在组织细胞中哪些基因是活跃表达的，哪些基因是低表达的，进而找到差异表达的基因。（本教程主要针对mRNA）
2. RNA-seq应用
## 二、实验技术
1. 实验原理和流程
   - 构建文库
     - RNA提取
     - RNA打断
     - 反转录
     - 添加接头
     - PCR扩展
     - 质量控制
   - 上机测序
     - 二代测序原理（Illumina）
     - 简单的找找差异基因不分析转录本剪切用二代测序技术就可以。
## 三、RNA-seq分析流程
### 3.1 准备工作
1. 主要的软件和工具
   1. 质控软件
   2. 比对软件
   3. 基因表达定量
   - 早期使用tophat和cufflinks；改进后的有HISAT2（或者STAR，虽然慢但更精准）、Stringtie（HTSeq或featureCounts）和Ballgown、gffcompare软件；
   - <a href="https://imgse.com/i/pEnGSv8"><img src="https://s21.ax1x.com/2025/02/10/pEnGSv8.png" alt="pEnGSv8.png" border="0"></a>
2. 教程
   - [b站Dlab视频教程——很多软件使用和详细代码](https://www.bilibili.com/video/BV1Jt4y157qA?spm_id_from=333.788.videopod.episodes&vd_source=2523c7055f0985a7f47ca59739b6b086&p=3)
   - [b站数据科学那些事——很多原理与实验设计的知识——2021年](https://www.bilibili.com/video/BV1t34y1U7zW/?spm_id_from=333.337.search-card.all.click&vd_source=2523c7055f0985a7f47ca59739b6b086)
   - [RNA-seq转录组数据分析入门实战](https://www.bilibili.com/video/BV1KJ411p7WN?spm_id_from=333.788.player.switch&vd_source=2523c7055f0985a7f47ca59739b6b086&p=6)
   - [【生信技能树】转录组测序数据分析](https://www.bilibili.com/video/BV12s41137HY?spm_id_from=333.788.videopod.episodes&vd_source=2523c7055f0985a7f47ca59739b6b086&p=4)
   - [学徒第2月，RNA-seq数据分析实战训练](https://mubu.com/doc/38y7pmgzLg)
   - [小白学生信（一）](https://mp.weixin.qq.com/s/_lc-pEMeQP-pSATWKVhITA)
   - [小白学生信（二）](https://mp.weixin.qq.com/s/q-wKhmPuDc968eGlttTHHA)
### 3.2 具体分析流程
1. 原始数据上传
    - 有时是自己的数据，就需要本地上传到服务器，这时候需要注意传输过程中的数据完整性； 
    - 使用md5值进行检验数据完整性：
        - 给自己的文件生成md5值：`md5sum *gz > md5.txt`(一个文件一个值)
        - 比对已有的md5值（就是传输之前要生成一次md5值，用来在传输之后进行比对）：`md5sum -c md5.txt`
2. 原始数据下载（如果不是直接得到的话）
    1. 相关知识
        1. [GEO和SRA数据库](https://phantom-aria.github.io/2022/05/04/a.html)
            - [GEO数据库挖掘，基因表达矩阵的下载与生成（R语言）--看看前面的文件介绍就行](https://www.bilibili.com/video/BV1A94y1f7Ec/?spm_id_from=333.337.search-card.all.click&vd_source=2523c7055f0985a7f47ca59739b6b086)
            - GEO数据库是基因表达数据库，一般提供的都是中间文件，比如转录组数据的表达矩阵（即最下面提供的series_matrix.txt，其中以！开头的都是项目信息和样本信息），芯片数据的中间注释文件，单细胞测序的细胞注释，突变的vcf突变文件等等。部分文章只提供SRA。（点击`SRA run selector`进入样本下载界面） 
           - SRA数据库，存储原始测序数据的数据库
        2. GEO数据库的GPL（platform）、GSM（Sample）、GSE（Series）
           - 文章提供的是NCBI的GSE number：比如练习数据GSE142570；点击链接进入后可以看到以GPL、GSE、GSM开头的编号；其中GPL表示测序用的平台、GSE代表一系列相关样本组成的数据集、GSM代表单个样本的数据记录，还包含该样本的的详细元数据（如实验条件、处理方式、生物学重复等），一个GSM可以是单独的样本，也可以是多样本的实验组。也就是说GSE和GSM是一对多的关系，每个GSE包含多个GSM。
           - 比如练习数据有513个Sample（但是文中说的是510个数据），也就是对应了513个GSM编号；共有ChIP-seq（355 samples）、RNA-seq（58）、FAIRE-seq（58）、BiSulfite-seq（38）、ChIP-reChIP-seq（4）5个数据集，即对应5个GSE编号，共513个样本。
        3. SRA数据库的SRP（Project）、SRS(样本 Sample)、SRX(数据产生 Experiment)、SRR（Read Run）
           - SRR是一次测序的数据，与GSM的关系不大。一个GSM可能对应多个SRR（一个样本测多次），一个SRR也可能对应多个GSM的数据（一次测多个样本）。
           - SRP代表一个实验项目，包含多个SRR。
    2. 测序数据下载（下载完后注意数据完整性，可以比较文件大小以及查看日志有无报错）
        - 本地下载
            - [利用TBtools转换XML文件得到网址列表](https://www.bilibili.com/video/BV19u411k7GJ/?spm_id_from=333.337.search-card.all.click&vd_source=2523c7055f0985a7f47ca59739b6b086)
            - 利用excel快速获取生成项目中所有的SRA序列号文件:视频已经不见，不太重要
        - 服务器下载
            - 教程 
                - [NCBI下载SRA数据的4种方法-简书](https://www.jianshu.com/p/0694fcb77157)
                - [prefetch批量下载SRA【Linux】_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1ig4y1F7zh/?spm_id_from=333.337.search-card.all.click&vd_source=2523c7055f0985a7f47ca59739b6b086)
                - [利用sratools工具批量下载SRR数据](https://mp.weixin.qq.com/s/cU8PjrIzUUzDsXbbzSUi3A)
                - [使用Aspera批量下载二代测序数据](https://mp.weixin.qq.com/s/pXNcEIzHFxl6bTLFNR1Fog)
                - [aspera不能独立使用-2021](https://www.cnblogs.com/miyuanbiotech/p/14366766.html)
                - [如何通过Aspera下载SRA或Fastq文件？（2022版）](https://zhuanlan.zhihu.com/p/565145928)
                - [Linux系统上安装aspera并用其批量高速下载转录组数据](https://zhuanlan.zhihu.com/p/640962215)
                - [aspera的高速下载确实很快吗](https://mp.weixin.qq.com/s/oXddBbeH4qGnjYw6n_H3fQ)
                - [生信公共数据库下载处理](https://www.cnblogs.com/cauwj/p/18137157)
            - 利用sra-tools软件的prefetch工具--NCBI官方出品、慢但稳定且简单
                - 安装软件
                    - [代码或网页安装](https://mp.weixin.qq.com/s/txaYGsXzPwGF3dc_vZ7jpg)
                - 主要命令：prefetch -f no -p [SRA文件序列号]
                    - -f no：表示不覆盖
                    - -p：显示进度
                    - --option-file <file_name，如txt文件>：批量下载该文件的SRA数据：
                    - 写成sh脚本是bash运行而不是sh命令；
                    -  文件示例
                        - <a href="https://imgse.com/i/pEnW6KO"><img src="https://s21.ax1x.com/2025/02/11/pEnW6KO.png" alt="pEnW6KO.png" border="0" /></a>
            - 利用Aspera软件下载SRR数据（权限带来的无法解决的问题，暂不用）
            - 安装软件
                - conda install -y -c hcc aspera-cli（在download_srr这个环境）
                - ascp --help
            - 密钥位置：/gss1/home/huangju02/.aspera/connect/etc/asperaweb_id_dsa.openssh
            - 主要命令
                - ascp -i ~/.aspera/connect/etc/asperaweb_id_dsa.openssh -l 300M -T -P33001 -k1 --mode recv --host ftp-private.ncbi.nlm.nih.gov --user era-fasp --file-list download.list . 
            - 报错（无权限故无法解决）
                - [NCBI和ENA转录组数据的下载-prefetch和aspera方法](https://mp.weixin.qq.com/s/5uiQQpNIWN635DrZfuXMMQ)
                - [ascp:Failed to open TCP connection for SSH, Exiting.](https://www.zhouxiaokun.com/archives/564)
    3. 处理成fastq文件
        - 教程
            - [sratools/fastq-dump从SRA文件中抽提fastq文件-2017](https://blog.csdn.net/u011262253/article/details/78459389) 
            - [sra转fastq三种工具-2022](https://mp.weixin.qq.com/s/UcPDCSRQAVZQ_csPw8wI1A)
            - [SRA到fastq格式的批量转换2种工具-2021](https://blog.csdn.net/qq_42458954/article/details/120018000)
            - [10x单细胞转录组测序sra转fastq踩坑](https://github.com/ixxmu/mp_duty/issues/1407)
        - 使用fastq-dump（NCBI官方的sratools的命令，老旧但也能用） 
            - 主要命令：`fastq-dump --split-3 SRRxxxx -O fastq`
                - --split-3：还有两个同样作用的参数--split-spot、--split-files，这两个好像都是针对paired的reads（files好像出现的较多），而--split-3既可以针对single end也可以针对paired end的数据。
                - -O ${directory}：输出文件所在路径
                - --fasta：可以直接转成fasta文件
                - --gzip：可以将输出的fastq文件压缩成gzip格式以节省空间
                - 对SRRxxxx还是SRRxxxx.sra效果是一样的
        - 使用fasterq-dump（也是NCBI的sratools，较新）
            -  主要命令：`fasterq-dump -p -e 24 --split-files -O ${outdirectory} SRR1234567.sra`
                - -p显示进程；-e支持线程数的控制；不想显示进程可以用-q参数；
                - 不支持压缩命令
                - 这个好像需要更大的空余内存，同样的命令有小报错'Disk quota exceeded'，快好像没有体会到。
                - 对SRRxxxx还是SRRxxxx.sra效果也是一样的
                - 同样支持--fasta参数直接转成fasta文件
3. 原始数据质控和比对（trim之后可以再进行一次fastqc
    1. 相关知识 
    2. 质量评估（也是质控的一环）
        - 教程
            - [序列数据上游分析——RNA-seq(fastqc+multiqc+trim_galore+hisat2+featureCounts-2022-12](https://mp.weixin.qq.com/s/qx5tDkm2ouOSz1D5_3AHGQ) 
            - [【转录组上游】全流程示例(fastQC+trim_galore+star+)](https://mp.weixin.qq.com/s/kv9Ku0M8nd8o8KBulU81RQ)
            - [RNAseq分析之FastQC（有结果解读）-2023-11](https://mp.weixin.qq.com/s/RQSCPyMgbeBweFjX92iCPA)
            - [一文掌握fastqc使用及结果解读-2024](https://mp.weixin.qq.com/s/clC40jVPcYpVAflgFRJCTA)
            - [软件2 —— fastqc和multiqc(及结果解读)-2023](https://mp.weixin.qq.com/s/J35UysO6Rwo22K9l6YL-pw)    
        - 使用FastQC软件进行质量评估
            - 可以多线程地对各种测序数据进行QC，conda一键安装； 
            - 主要命令：`fastqc -t 12 -o {out_path} sample1_1.fq sample1_2.fq`
                - -t指定线程数；
                - -o指定存放结果文件的目录路径；最后输出一个html文件和一个zip文件
            - FastQC结果解读
                1. Basic Statistics
                    - 就是整体的一个统计，可以看到GC含量和reads读长； 
                2. Per base sequence quality
                    - 展示fastq文件中每个位置上的所有reads的碱基质量的一个箱线图。假设文件有22783596个read，那么第一个位置上的base(碱基)就有22783596个质量值，这个值画成第一个箱线图。
                    - 全部在绿色以上就可以吗？ 
                3. Per sequence quality scores
                    - 可以查看序列的子集是否具有普遍较低的质量值。横轴：质量值0-40 ，也即是Q值纵轴：每个质量值对应的read数。如果图中测序结果主要集中在高分中，说明测序质量良好。（和第2部分差不多） 
                4. Per base sequence content
                    - 每个碱基位置上：ATGC含量的分布图。碱基类型分布检查用于检测有无AT、GC分离现象，由于RNA-Seq所测的序列为随机打断的cDNA片段，因随机性打断及碱基互补配对原则，理论上，G和C、A和T的含量每个测序循环上应分别相等，且整个测序过程稳定不变，呈水平线。由于Reads 5'端的前几个碱基为随机引物序列存在一定的偏好性，因此会在碱基分布图中出现前端波动较大的现象。
                        - 横轴：各碱基位置；
                        - 纵轴：碱基百分比
                    - 图上展示了在所有读取序列的每个位置上，四种核苷酸的比例。初始位置显示了显著的变异和不平衡，这是正常的，在序列的1-9位，通常会出现较大的波动。在第10位之后，线条趋于变得更稳定，但仍有一些波动，这表明文库可能存在轻微的偏差，或者测序过程中存在导致碱基分布不均的问题（质控之后就会好很多）。   
                5. Per sequence GC content
                    - 此模块评估文件中每个序列的整个长度的GC含量，并将其与建模的正态分布GC含量进行比较。横轴：GC含量百分比；纵轴：每个GC含量对应的序列数量；
                    - 图上的曲线表示在所有测序读取中，每个读取序列的平均GC含量的分布（通常假设是正态分布）。蓝线为理论分布；红线为测量值，二者越接近越好。
                    - 在这张图中，红色实际分布线条在中间的GC含量区间与蓝色理论分布线条较为吻合，表明在这个区间内的GC含量是符合预期的。然而，在高GC含量区间，实际分布的读取数量超过了理论分布预期的数量，这可能表明在高GC含量的区域有过量的读取。这种情况可能是由于样本中某些富含GC的区域被过度测序，或者是测序过程中的某些偏差导致。 
                6. Per base N content
                    - N含量分布图，理想情况下，整个读取中的N含量应该是非常低的，因为N代表一个位置上碱基的不确定性，通常是由于测序仪器无法确定该位置的碱基是A、T、C还是G。横轴：读取序列中的位置（以碱基对为单位），纵轴：该位置上N的百分比。
                    - 图中，整个读取长度范围内的N含量几乎为零，这表示质量很好。 
                7.  Sequence Length Distribution
                    - reads 长度分布图。通常情况下，这将生成一个简单的图表，只显示一个大小的峰值，但是对于可变长度的FastQ文件，这将显示每个不同大小的序列片段的相对数量。横轴：读取的长度，单位是碱基对（bp）纵轴：在每个长度区间内reads的数量在图中，可以看到几乎所有的读取都集中在一个非常窄的长度区间（图上是145-149bp）。这种情况通常意味着测序读取的长度非常一致，这在使用特定长度的测序策略时是预期的结果。长度的一致性通常对后续分析有利，例如在进行序列组装或映射到参考基因组时。 
                8.  Sequence Duplication Levels
                    - 序列重复性分布图横坐标表示duplication 的次数。例如1表示序列没有重复，2表示序列重复了一次，以此类推。“>1”表示重复一次以上的序列，“>10”表示重复超过10次的序列，依此类推，直至“>10k”表示重复超过10,000次的序列。纵坐标是重复序列占总序列的百分比在理想的RNAseq数据中，大多数序列都应该是唯一的，因为它们代表了从不同转录本中捕获的RNA片段。然而，一些RNA片段可能由于生物学上的原因或者技术上的原因（如PCR扩增）出现重复。从图中可以看到，大部分序列只出现一次（没有重复），这是好的。但是，也有一定比例的序列出现了多次重复，特别是在某些较高的重复水平区域（如9次、>10次），这些峰值表示特定序列高频出现。图上方的文字“Percent of seqs remaining if deduplicated 8.31%”意味着如果去除所有的重复序列，还会剩下8.31%的唯一序列。这个比例较低，可能表明在测序数据中存在大量的重复。这种高水平的重复可能是由于高表达基因造成的，也可能是文库制备或测序过程中的偏差。这种大量重复在RNAseq中是正常的。 
                9.  Overrepresented sequences
                     - 表中的“序列”列显示了具体的核苷酸序列。"计数"列显示了每个序列在数据中出现的次数，而“百分比”列显示了该序列出现次数占总测序读取的百分比。最后，“可能来源”列显示了这些序列可能与已知序列的匹配情况，这里都显示为"No Hit"，意味着没有找到与这些高频序列相匹配的已知来源。过量表达的序列可能意味着：有些序列可能是由于技术原因过度扩增，如PCR过程中的偏好性扩增。它们可能来源于RNA样本中表达水平特别高的基因。也可能是污染或者其他实验室技术问题的结果。 
                10. Adapter Content
                    - 该图用于检测测序读取中是否存在接头序列。在测序过程中，接头序列被添加到DNA片段的两端，以便将片段固定到测序平台上。理想情况下，这些接头序列在数据处理之前会被去除，因为它们不是样本的一部分。然而，如果接头剪切不完全，它们可能会在测序数据中出现。横轴：表示读取序列中的位置纵轴：检测到接头序列的百分比。不同颜色代表了不同的接头类型，例如Illumina Universal Adapter、Illumina Small RNA 3' Adapter等。软件内置了四种常用的测序接头序列，同时fastqc也有一个参数-a可以自定义接头序列从上图中可以看出，在所有读取位置上几乎没有检测到接头序列，这意味着数据中的接头含量非常低，或者说几乎没有接头污染。表明接头序列已被有效去除，减少了对后续数据分析造成干扰的可能性。 
        - 使用MultiQC软件汇总成一个报告
            - 简介与安装
                - 用fastQC进行质量评估后，用MultiQC整合多个样本的分析结果成一个html报告，运行时可以提供一个或多个目录以扫描分析结果。
                - 安装：不论是使用conda还是pip安装后都无法正常使用，使用`multiqc --version`都会出现如下的报错，使用python3.8还是python3.13都无济于事，重新安装numpy也没用，并且公共软件的multiqc也同样的报错；最后是在自己本地`pip install multiqc`安装并运行成功
                    - [官网](https://docs.seqera.io/multiqc/getting_started/installation) 
                    - `ModuleNotFoundError: No module named 'numpy.core._multiarray_umath'`
                    - `ImportError: Error importing numpy: you should not try to import numpy from its source directory; please exit the numpy source tree, and relaunch your python interpreter from there.` 
                    - <a href="https://imgse.com/i/pEQcinS"><img src="https://s21.ax1x.com/2025/02/20/pEQcinS.png" alt="pEQcinS.png" border="0"></a>
            - 主要命令：`multiqc ./*.zip -o ./ > ./multiqc.log &`
                - ./*.zip：就是fastqc结果路径，注意，multiqc接受的是fastqc结果的zip文件；
                - -o：就是输出目录，默认有一个multiqc_data文件夹和html文件产生；
            - multiqc结果解读
                - [MultiQC对FastQC结果的解读](https://mp.weixin.qq.com/s/ZgX5fpjqRD1UCtBxpf6ytw) 
                - [MultiQC对FastQC结果的解读](https://www.bilibili.com/video/BV1QG411d7RN/?spm_id_from=333.1391.0.0&vd_source=2523c7055f0985a7f47ca59739b6b086)
                - 首先包含一个general statistics，是一个整体概览；
                - <a href="https://imgse.com/i/pEQcF0g"><img src="https://s21.ax1x.com/2025/02/20/pEQcF0g.png" alt="pEQcF0g.png" border="0"></a>
    3. 质控
        - 教程
            - [测序数据质量控制之Trimmomatic-2017-10](https://mp.weixin.qq.com/s/f7MZ0yRD9tMjMQYljO1exQ)
            - [数据预处理：Cutadapt、FastP、Trimmomatic 怎么选？-2025-02(建议使用fastp)](https://mp.weixin.qq.com/s/WAA39p1wCPHRqm-cUonAIA)
            - [测序数据清洗篇 Ⅰ（含接头）-2024-05](https://mp.weixin.qq.com/s/O_zClrRZO8I6SHwbpuM1KQ)
            - [软件3 —— Trim Galore和Trimmomatic（含接头）-2023-06](https://mp.weixin.qq.com/s/5pEiqS5l4HQzptmKZOlm0w)
            - [转录组上游质控trim galore安装与使用-2024-06](https://mp.weixin.qq.com/s/Al_nysdFWblzbfJhl9-EtA)
            - [功能强大的trim galore软件-2024-02](https://mp.weixin.qq.com/s/SlSR7KB_PdaOqC68qMkHxg)
            - [24转录组分析_trim_galore软件的使用方法_2021-06(未看)](https://www.bilibili.com/video/BV1744y1z7fm?spm_id_from=333.788.recommend_more_video.1&vd_source=2523c7055f0985a7f47ca59739b6b086)
            - [数据质控软件知多少-fastp-2025-02](https://mp.weixin.qq.com/s/Hd4SxmuapOSLOiaaWf_j5w)
            - [fastp—FastQ文件一体化预处理工具(含结果解读）-2024-04](https://mp.weixin.qq.com/s/lVmY250wYgLT1JITE6I8Hw)
            - [fastq文件预处理神器——fastp-2024-07](https://mp.weixin.qq.com/s/5u-2zDFW1iawzzi2IjYF1g)
        - 使用fastp软件进行质控
            - 简介与安装
                - 应该是比较新的，比较快；但是有文献显示fastp去接头不如trimmomatic干净。
                - conda一键安装
            - 简单用法：
                - 主要命令（双端）：`fastp [-i|--in1] in.R1.fq.gz [-I|--in2] in.R2.fq.gz [-o|--out1] out.R1.fq.gz [-O|--out2] out.R2.fq.gz --adapter_fasta adapter.fa --thread 10 -h A_filter.html`
                    - -a|--adapter_fasta：针对read1和read2去接头的接头文件
                    - -w|--thread：设置线程数
                    - -h|--html：过滤报告，含过滤前后的数据统计和一些图表展示；
                    - -j|--json: json格式的报告；
                    - -q|--qualified_quality_phred:设定碱基合格的质量阈值，如20；
                    - -l|--length_required：设定reads的最小长度要求，（默认值是15，常用为36），所有碱基长度小于该值的reads将被丢弃；如果要过滤长序列可以使用--length_limit参数（比如小RNA）；
                    - -n|--n_base_limit：设定reads中允许的最大未知碱基（N）数量。默认值为5（常用为3），即如果一个读段中的N的数量超过5，那么这个读段（或在配对末端测序中的读段对）将被丢弃；
                    - -5|--cut_front、-3|--cut_tail、-r|--cut_right同Trimmomatic的LEADING TRAILING 和SLIDINGWINDOW 这三个参数，有需要可以设置。
                    - -M|--cut_mean_quality 可以用于设置滑窗需要满足的平均质量。
                - 主要命令（单端）：`fastp -i in.fq -o out.fq`
        - 使用trimmonmatic软件进行质控
            - 简介与安装
                - Trimmomatic是一个处理高通量测序数据常用的工具，尤其是对于Illumina测序数据。它提供了包括去除接头序列（adapter trimming）、质量过滤（quality filtering）、去除低质量序列（trimming low-quality bases）等在内的功能，以帮助提高序列数据的质量和可靠性。
                - FastQC并不包含真正质量控制的过程（不会改变reads本身）；这时候就需要trimmomatic之类的软件了，它支持多线程，可以针对SE和PE（单端和双端）测序数据，也支持gzip和bzip2压缩文件。
                - 可以conda一键安装；我在rna-seq环境中安装过，但是用`find /gss1/home/huangju02/anaconda3/envs/ -name *rimmomatic*`去查找会发现有一个trimmomatic和一个trimmomatic.jar，运行前者实际调用的是后者；
            - 简单用法
                - 由于Trimmomatic过滤数据的步骤与命令行中过滤参数的顺序有关，因此，如果需要去接头，建议第一步就去接头，否则接头序列被其他的过滤参数剪切掉部分之后就更难匹配更难去除干净。建议将文件路径加到环境变量（如果用conda就可以不用）；phred33对应(Sanger/Illumina 1.9+ encoding)，-phred64对应(Illumina 1.5 encoding)；trimmomatic v3.2版本之后会自动确定base quality encoding是-phred64还是-phred33；
                - 主要命令（SE模式）：`java -jar {path_to trimmomatic} SE -threads {threads} [-phred33|-phred64] [-trimlog {logFile}] {input} {output} {step 1} {step 2} ...`
                    - SE表示使用针对单端测序；-threads指定线程数；
                    - -trimlog参数指定了过滤日志文件名。
                        - 日志中包含以下内容:read ID、过滤之后剩余序列长度、过滤之后的序列起始碱基位置（序列开头处被切掉了多少个碱基）、过滤之后的序列末端碱基位置、序列末端处被剪切掉的碱基数。由于生成的 trimlog 文件中包含了每一条 reads 的处理记录，因此文件体积巨大（GB 级别），如果后面不会用到 trim 日志，建议不要使用这个参数。
                - 主要命令（PE模式）：`java -jar {path_to trimmomatic} PE [-phred33|-phred64] ${name}_1.fq.gz ${name}_2.fq.gz ${name}_R1.clean.fq.gz ${name}_R1.unpaired.fq.gz ${name}_R2.clean.fq.gz ${name}_R2.unpaired.fq.gz ILLUMINACLIP:"$adapter"/Exome.fa:2:30:10:1:true LEADING:20 TRAILING:20 SLIDINGWINDOW:4:15 -threads 2 MINLEN:36`
                    - 在 PE 模式下，有两个输入文件，正向测序序列和反向测序序列，但是过滤之后输出文件有四个，过滤之后双端序列都保留的就是 paired，反之如果其中一端序列过滤之后被丢弃了另一端序列保留下来了就是unpaired。
                        - 一张图片  
                    - `${name}1.fq.gz ${name}2.fq.gz` 为输入文件；`${name}R1.clean.fq.gz ${name}R1.unpaired.fq.gz ${name}R2.clean.fq.gz ${name}R2.unpaired.fq.gz` 为对应的四个输出文件；
                    - `ILLUMINACLIP:"$adapter"/Exome.fa:2:30:10:1:true`，这部分指定2种去接头模式的参数：`"$adapter"/Exome.fa` 指明需要匹配的接头fasta文件；2 代表 16 个碱基长度的种子序列中可以有 2 个错配，30 代表采用回文模式时匹配得分至少为30 (约50个碱基)，10 代表采用简单模式时匹配得分至少为10 (约17个碱基)；可以根据FastQC结果的adapter部分选取，如果结果好的话可以不用本参数。双端的话最好把true用上；
                    - `LEADING: 20`，从序列的开头开始去掉质量值小于 20 的碱基；
                    - `TRAILING:20`，从序列的末尾开始去掉质量值小于 20 的碱基；
                    - `SLIDINGWINDOW:4:15`，从 5' 端开始以 4 bp 的窗口计算碱基平均质量，如果此窗口平均值低于 15，则从这个位置截断read；
                    - `MINLEN:36`， 如果 reads 长度小于 36 bp 则扔掉整条 read。
        - 使用trim_galore软件进行质控
            - 简介与安装
                - cutadapt软件可以对NGS数据进行质量过滤，FastQC软件可以查看NGS数据的质量分布.而Trim Galore这个神器将Cutadapt和FastQC这两款软件封装到一起，使得质控工作更加简便高效。无论是Illumina、Nextera还是smallRNA测序平台的双端或单端数据，甚至RRBS (Reduced Representation Bisulfite-Seq) 数据，Trim Galore都能轻松应对。
                - 可以直接conda安装
            - 简单用法
                - Trim Galore的工作流程主要包括两步：第一步是去除低质量碱基，第二步是去除3'末端的adapter。如果你没有指定具体的adapter，程序会自动检测前1百万条序列并对比前12-13bp的序列来判断适合的adapter类型.
                - 主要命令：`nohup trim_galore --paired -q 25 --phred33 --length 35 --stringency 3 -o ./ $fq1 $fq2 &`
                    - -q 25：设定Phred质量评分阈值为25。
                    - --phred33：指定使用phred33碱基质量值体系。
                    - --length 35：输出reads长度阈值，小于35bp的reads会被抛弃。
                    - --stringency 3：可以容忍的前后adapter重叠的碱基数为3。
                    - --paired：针对双端测序结果，单端测序直接省略，以及接受一个输入文件即可。
                    - -o：指定输出目录；-j可指定线程数，还是内核？反正两者是在一台主机上成正比的参数，而节点数则是主机数；
                    - --fastqc:可以质控完后运行FastQC，使用--fastqc_args指定传递给FastQC的参数。
                    - --gzip：将输出结果压缩为gzip格式。
                    - --max_n：一条read在被完全删除之前可能包含的N的总数，别人的示例为4。
                    - -e设置最大错误率：默认为0.1，应该是比较常用的。   
    4. 比对（此处主要指有参比对，而且是基于基因组比对，无参比对可以用Trinity，基于转录本比对可以用RSEM，各个比对软件构建的index不能互用，能输出成bam就不要输出成sam，而且后缀是sam内容不一定是sam，比如subjunc软件默认输出的就是bam文件；比对之后有一个samtools flagstat命令可以进行比对结果的评估）
        - 相关知识
            - RNA-seq的reads是跨外显子的（剔除内含子），所以不能用DNA测序所用的BWA、bowtie，它们它们假设 reads 来自于连续的 DNA 序列，不会考虑外显子与内含子的边界问题，无法识别剪接事件；
            - 常用的软件有star、hisat2（基于bowtie2）、tophat（基于bowtie，tophat2基于bowtie2）、subread；
            - 大多数物种的mark的ratio（也就是比对到参考基因组的reads比例）在75%-90%；
        - 教程
            - [Tophat2比对原理及命令-2018-07](https://mp.weixin.qq.com/s/v553kZ8SrpOxhvm6kIultQ)
            - [使用tophat2和cufflinks组合进行有参转录组分析(较详细,还涉及安装的报错处理)-2023-02](https://mp.weixin.qq.com/s/EtYSc_q1gyDDu9DZxR7VGA)
            - [Tophat2 & Bowtie2 Tutorial (MAC)(视频教程）-2015-08](https://www.youtube.com/watch?v=7_yY-PuVN3U)
            - [生信软件|bowtie2（测序序列与参考序列比对）-2023-06](https://blog.csdn.net/u011262253/article/details/79833969)
            - [转录组学助力：索引文件构建(tophat2、star、hisat2简单使用)-2023-11](https://mp.weixin.qq.com/s/9RdWTbu3XS5q1lwk_UitNg)
            - [转录组学初体验 ｜ 2.4 Tophat与Tophat2（参数--threads是不是写错的，还有tophat2比对的命令也有错误，不用.fa）-2024-06](https://mp.weixin.qq.com/s/VbXYw1a4MEzWXMYkcQSRcA)
            - [转录组学初体验 ｜ 2.5 软件介绍STAR](https://mp.weixin.qq.com/s/UVnNA-Mk-x7tGrUR3UlzrQ)
            - [STAR比对工具原理及使用介绍(含结果解读)-2022-11](https://mp.weixin.qq.com/s/lBIcIWMU7_5b_sj2I9WUFQ)
            - [RNA-Seq比对工具 | STAR-2024-10](https://mp.weixin.qq.com/s/VsjcQ2Vrb-k9wfaarBfQgg)
            - [RNA-seq上游分析 ① | STAR流程测试-2024-06](https://mp.weixin.qq.com/s/qvhSc-OKYhK-gcA5uNlk5g)
            - [RNA-seq数据分析—STAR比对-2023-03](https://mp.weixin.qq.com/s/jyMCPOg364EP3ayze24q6g)
            - [RNA-seq分析——数据比对](https://mp.weixin.qq.com/s/p1pwdliUUpj9dNa5h26B2A)
        - 使用tophat2软件进行比对(目前不太用，速度慢，到2016年已停止更新)
            1. 在基因组网站上下载基因组文件和对应的注释文件
                - yotube视频直接在ensembl上下载的是genome.fa而不是toplevel，而且这个细菌基因组很小，建立索引时直接一个文件名和前缀就完了；
                - 水稻MH63应该下载的是dna_index下面的toplevel.fa.gz那个吧。ensembl上的注释文件倒是只有一个。具体要参考文献吧。
            2. 使用bowtie2软件来对基因组文件建立索引（5-10min）
                - 简介与安装
                    - 想要使用tophat2，首先要下载待研究物种的bowtie index文件，bowtie网站提供了一些物种的index文件，如果没有（水稻只有一个build4版本），需要先用bowtie2(bowtie2-build)建立你的index文件。 
                    - 和bwa一样用于短reads的比对软件，bowtie主要用于50-1000bp的reads进行比对，生产SAM文件。
                    - 直接用conda安装（出现HTTP error，可改用直接在login端口下载而不是在compute01端口）。
                - 简单用法
                    - 主要命令：`bowtie2-build [options] <reference_genome> <genome_index_prefix>`（可以输入`bowtie2-build -h`查看该子命令帮助文档）
                        - -f:指定参考基因组文件为fasta格式，默认就是，所以可以不用写； 
                        - -threads|-p:运行线程数量（bowtie2-build好像改成了-t这个选项）；
                        - --large-index:使用较大的索引。一般情况下基因组大于4G的时候，考虑使用大索引。
                        - reference_genome:输入的基因组文件路径；
                        - genome_index_prefix：输出的索引文件的前缀，比如`xx/xx`；结束后得到6个索引文件例如BanmaoChr.1.bt2l、BanmaoChr.2.bt2l、BanmaoChr.3.bt2l、BanmaoChr.4.bt2l、BanmaoChr.rev.1.bt2l、BanmaoChr.rev.2.bt2l、BanmaoChr.fa；
                        - **注意**：索引文件和基因组文件前缀需要一致；而且最好将fasta的基因组文件和生成的索引文件放在一个目录下，不然tophat2运行过程还会自动生成，也就是可能有一个tophat_out文件夹产生，里面包含fasta的基因组文件（好像就算放在一起也会产生这个文件）。
            3. 使用tophat2进行比对
                - 简介与安装（源码安装）
                    - TopHat2的安装是依赖Bowtie2的（当然Bowtie1也是可行的）；TopHat2容错率很低，并不会因为没有比对上就而截短Read从而去比对上，所以低质量的碱基比对的效果可能不是那么好。此外，TopHat2可以察觉基因组的易位，将Read比对到潜在的融合转录子上。
                    - conda一键安装时注意直接写tophat而不是写tophat2，还是用login端口；tophat的安装除了依赖bowtie2还依赖samtools和boost，安装好了这俩还一直solving environment。直接使用源码安装（当然还是要下载bowtie2）：
                        - 安装教程：[ubuntu中tophat2安装（知乎）-2020-12](https://zhuanlan.zhihu.com/p/339781741)
                        - [tophat2官网中获取到右下角的Linux x86_64 binary的下载连接](https://ccb.jhu.edu/software/tophat/tutorial.shtml)
                        - 使用wget下载后，将路径添加到系统路径（`export PATH=path_to_/tophat-2.1.1.Linux_x86_64/:$PATH`），最好`source ~/.bashrc`一下；然后输入`tophat2 -h` 测试是否能正常使用，应该会报错`SyntaxError:invalid syntax`：
                            - <a href="https://imgse.com/i/pEuh7o4"><img src="https://s21.ax1x.com/2025/02/13/pEuh7o4.png" alt="pEuh7o4.png" border="0"></a>
                        - 原因应该是tophat2使用的是python2；解决方法如下：输入`whereis python2`找到系统中的python2.7的路径，我用的是`/usr/bin/python2.7`这个，如果后面报错无权限就再换一个路径即可；然后进入文件夹tophat-2.1.0.Linux_x86_64/下，`vim tophat`进入tophat配置文件，将`将#！/usr/bin/env python`修改成刚刚的`/usr/bin/python2.7`，保存后再次测试`tophat2 -h`验证是否成功。
                            - <a href="https://imgse.com/i/pEuhbFJ"><img src="https://s21.ax1x.com/2025/02/13/pEuhbFJ.png" alt="pEuhbFJ.png" border="0"></a>  
                - 简单用法
                    1. 直接进行mapping：`tophat2 -p 6 -G {species.gff3|species.gtf} -o {output_folder} {genome_index_prefix} {test_R1_cutadapt.fq.gz} {test_R2_cutadapt.fq.gz}`
                        - -p：线程数；
                        - -G：后面接相应参考基因组的注释文件的路径（gtf或gff），如果注释文件存在的话，在运行时会首先被tophat2调用bowtie2建立转录组的index（这个过程会占用一定的时间，建议事先准备好转录组索引以节约后续比对的时间），读长将会优先根据转录组索引进行比对；
                            - 生成的结果文件：GRCh37.74.tr.1.bt2、GRCh37.74.tr.2.bt2、GRCh37.74.tr.3.bt2、GRCh37.74.tr.4.bt2、GRCh37.74.tr.fa、GRCh37.74.tr.fa.tlst、GRCh37.74.tr.gff、GRCh37.74.tr.rev.1.bt2、GRCh37.74.tr.rev.2.bt2、GRCh37.74.tr.ver。
                        - -o：文件输出目录的路径；
                        - {genome_index_prefix}:基因组索引文件的目录路径+前缀，如`xx/xx/xxx`，此处还是要注意索引文件和基因组文件前缀需要一致；
                        - -r:成对的reads之间的平均inner距离，默认为50。
                        - --library-type  Tophat2处理的reads具有链特异性。一般Illumina数据的默认library-type为fr-unstranded。
                        - 记住最后两个是clean的文件；如果出现`Warning:could not find FASTA file bowtie2_index/xxx`可能就是你没有将基因组文件和基因组索引文件放在一个目录下；没啥太大影响；
                    2. 先生成转录组索引，再进行mapping:
                        - 主要命令：`tophat2 -G {species.gff3|species.gtf} --transcriptome-index={transcriptome_index_prefix} {genome_index_prefix}`
                            - -G：接注释文件(gff或gtf)；
                            - --transcriptome-index：接生成的一系列转录组索引文件的目录路径+前缀（教程示例是在{genome_index_prefix}的基础上加一个`.tr后缀`；
                            - {genome_index_prefix}:bowtie2生成的基因组索引的目录路径+前缀；
                            - **注意**：基因组索引里染色体的名称一定要和gtf或gff注释文件的里的相同，否则可能会报错`Couldn't build bowtie index with err=1`，可以用sed修改。
                        - 主要命令: `tophat2 –p 20 –o {output_folder} --transcriptome-index={transcriptome_index_prefix} {genome_index_prefix} {reads_1.fa} {reads_2.fa}`
                            - -o：文件输出目录的路径；
                            - .gz结尾压缩的Read是可以直接使用的，但是.tgz 或者 .tar.gz结尾的压缩文件是需要被解压后才能使用。
                    - tophat2生成的结果文件：
                        - accepted_hits.bam：以BAM文件的形式储存着比对信息，这些比对信息是依据染色体的顺序排列储存的；可用samtools查看，如果第一行中出现`SO:coordinate`——指bam文件**已经按照基因组坐标排序过了**；是后面cufflinks的输入文件。
                        - junctions.bed：是以Bed格式储存着发现的剪接位点，每个位点都含有左右两个部分，每个部分长度是Read能够跨越剪接位点匹配到基因组的最远距离，最终的得分是跨越剪接位点的Read数量。
                        - insertions.bed：包含着查询到的插入位点信息，chromLeft指的是插入到基因组的位置。
                        - deletions.bed：包含着查询到的缺失位点信息，chromLeft指的是缺失片段的第一个碱基。
                        - unmapped.bam：没有map上的序列
                        - align_summary.txt能够显示比对效率以及多少Read能够多处比对；`concordant pair alignment rate`显示比对率，一般75-90%，如果是某几条染色体则会相应降低。
        - 使用star软件进行比对（快且结果准确但占内存，笔电切勿使用）
            - 简介与安装
                - STAR是一个专门用于RNA-seq数据映射的比对工具，它在变异检测方面具有更好的灵敏度，被用于GATK最佳时间工作流程。和tophat一样也需要先构建基因组索引后进行比对。
                - conda一键安装。
            - 简单用法（超长的说明文档）
                1. 下载物种的参考基因组文件和注释文件
                2. 使用star软件建立index
                    - 主要命令：`STAR --runMode genomeGenerate [options] --genomeDir {index_folder}`
                        - --runMode genomeGenerate：运行模式，genomeGenerate模式用于构建基因组索引；不选的话默认是mapping；
                        - --runThreadN {int}：使用的线程数（处理器数） 
                        - --genomeDir {index_folder}：存储基因组索引的目录路径（提前建立），这里不需要前缀；
                        - --genomeFastaFiles {reference_genome}：FASTA格式的参考基因组文件路径；
                        - --sjdbGTFfile {species.gff3|species.gtf}：用于基因注释的GTF文件（可选）
                        - --sjdbOverhang 149：剪接位点周围的读取长度.对于sjdbOverhang参数，一般为读取长度-1（或最大读取长度-1）。例如，如果您的读取长度为150，该值应为149。在大多数情况下，默认值100也有效。
                    - 示例：
                        - `"STAR --runMode genomeGenerate --runThreadN 40 --genomeDir /home/zhangyk/project/RNA_seq/Result/genome_index --genomeFastaFiles /home/zhangyk/project/RNA_seq/data/referdata/hg38.fa --sjdbGTFfile /home/zhangyk/project/RNA_seq/data/referdata/hg38.ncbiRefSeq.gtf`
                        - `STAR --runMode genomeGenerate --genomeDir ~/reference/index/star/mm10 --genomeFastaFiles ~/reference/genome/mm10/mm10.fa --sjdbGTFfile ~/reference/gtf/gencode/gencode.vM12.annotation.gtf --sjdbOverhang 149 --runThreadN 4`
                3. 使用star比对
                    - 主要命令：`STAR --runMode alignReads [options]... --genomeDir {index_folder} --readFilesIn R1.fq R2.fq`
                        - --runThreadN {int}:线程数
                        - --genomeDir {index_folder}:建立好的index文件存放路径；
                        - --readFilesIn:输入的待比对的input fastq文件，也就是过了后的clean_data；
                        - --readFilesCommand zcat:当input fastq文件是压缩格式时，这里必须指定该参数，也可以用`--readFilesCommand gunzip -c`代替；
                        - --sjdbGTFfile {species.gff3|species.gtf}:基因组注释gtf文件路径，当提供该参数后，可以直接对mapping后的结果进行注释计数
                        - --outSAMtype BAM SortedByCoordinate:输出排序后的bam文件；
                        - --outBAMsortingThreadN {int}：排序使用的线程数；
                        - --outFileNamePrefix Output_prefix:输出文件的前缀名；
                            - 结果文件如下：1. Log.out: 记录详细的比对运行日志，包括参数设置、运行步骤、警告和错误信息。2. Log.progress.out: 展示每个阶段的reads数目和进度。3. SJ.out.tab: 列出所有检测到的剪接事件。4. Log.final.out: 提供最终的统计信息摘要。5. Aligned.sortedByCoord.out.bam：通过BAM压缩格式存储排序后的比对信息。BAM文件可以导入IGV等工具进行可视化。 
                        - --outSAMstrandField intronMotif：3'端和5'端偏好性，这样写就行；
                        - --outSAMattributes All：要选ALL，不然下游软件会报错;
                        - --outFilterIntronMotifs RemoveNoncanonical：删除非典型，仅保留典型
                        - --quantMode GeneCounts:计数模式，按基因进行计数，使用htseq-count的默认参数进行计数。前提是指定了--sjdbGTFfile；如果后面要使用RSEM进行定量分析，可以再加一个TranscriptomeSAM；
                    - 示例：`STAR --runThreadN 7 --runMode alignReads --readFilesCommand zcat --quantMode TranscriptomeSAM GeneCounts --twopassMode Basic --outSAMtype BAM Unsorted --outSAMunmapped None --genomeDir /home/zhangyk/project/RNA_seq/Result/genome_index/ --readFilesIn /home/zhangyk/project/RNA_seq/Result/trimmomatic_result/${j%_*_*}_R1_paired.fastq.gz /home/zhangyk/project/RNA_seq/Result/trimmomatic_result/${j%_*_*}_R2_paied.fastq.gz --outFileNamePrefix /home/zhangyk/project/RNA_seq/Result/star_result/STAR_${j%_*_*}` 
        - <a href="https://imgse.com/i/pEnG9KS"><img src="https://s21.ax1x.com/2025/02/10/pEnG9KS.png" alt="pEnG9KS.png" border="0"></a>
        - 使用Hisat2软件进行比对（效率高内存少，但输出结果仅为比对文件）
        - 比对之后的结果评估
            - 使用samtools flagstat对bam文件进行统计
                - 主要命令：`ls *.bam | while read id; do (samtools flagstat -@ 10 $id $(basename ${id} ". bam").flagstat ); done`
            - 对flagstat结果进行整合
                - 使用awk命令:
                    - `cat *| awk '{print $2}' | paste - - - - - - -`：-个数应该是样本个数吧
                    - `cat *| awk '{print $1}' | paste - - - -- -`
                - 使用multiqc也可以对目录下的flagstat的结果进行整合：直接使用`multiqc ./`；multiqc建议看一下官方文档；  
4. 表达定量
    - 相关知识
        - FPKM、RPKM、TPM
    - 教程
    - 处理原始比对文件的步骤
        - sam格式转bam格式
        - 对bam文件进行排序
        - 去除比对得分较低序列
        - 去除重复reads
    - 使用cufflinks软件进行表达量计算（老，不常用了，效率不高）
        - 教程：
            - [转录组分析（四）tophat+cufflinks篇](https://zhuanlan.zhihu.com/p/561285944)
            - [github-cufflinks文档](https://github.com/cole-trapnell-lab/cufflinks/blob/master/doc/html/manual.html)
            - [完整转录组RNAseq分析流程（tophat2+cufflink+cuffdiff）(有自己的代码，还算详细，)--2020-03](https://www.jianshu.com/p/0ab0e2aeca14)
            - [转录组分析（四）tophat+cufflinks篇（有代码）-2022-09](https://zhuanlan.zhihu.com/p/561285944)
            - [RiceENCODE-pipelin（比较粗略）](https://github.com/lxie-0709/RiceENCODE-pipeline/blob/main/RNA-seq.sh)
            - [RNA-seq数据分析（二）——Cufflinks篇（作用不是很大）--2021-03](https://blog.51cto.com/u_15127592/2674975)
            - [Cufflinks的使用(基本上只是官方文档的翻译--2021-06)](https://www.jianshu.com/p/82d6116fd0b8)
        - 简介与安装
            - 在使用tophat进行比对之后，你需要了解转录本的丰度，它首先将比对后的序列组装成转录本然后对转录本进行定量。
                - Assembly（将短的reads组装成长的转录本）：比对结果包含所有序列在参考基因组的位置和match信息，转录本组装的第一步就是识别标记相互不兼容的片段，这些片段不能共存于同一个转录本中；第二步就是通过overlap来多个片段连接起来；第三步就是将前两个过程得到的片段信息整合，最终构建出每个转录本的不同异构体（isoforms）；
                    - <a href="https://imgse.com/i/pEMr2DJ"><img src="https://s21.ax1x.com/2025/02/18/pEMr2DJ.png" alt="pEMr2DJ.png" border="0"></a>  
                - Abundance estimation：接下来根据片段长度分布 和最大似然法（Maximum Likelihood）将所有reads分配到正确的转录本异构体上；然后计算FPKM；
                    - <a href="https://imgse.com/i/pEMrRb9"><img src="https://s21.ax1x.com/2025/02/18/pEMrRb9.png" alt="pEMrRb9.png" border="0"></a>
            - cufflinks软件包括cufflinks（组装转录本、定量）, cuffmerge, cuffcompare, cuffquant, cuffnorm和cuffdiff（表达水平）等几项主要的功能；主要是根据Tophat2产生的比对结果，依托或不依托于参考基因组的GTF注释文件，计算出(各个gene的)isoform的FPKM值，并给出trascripts.gtf 注释结果(组装出转录组)。
                - cufflinks部分对RNA-seq数据进行转录本组装和表达定量；
                - cufflmerge部分将多个样本得到的转录本整合成一套完整的转录本集合，可以去除冗余；
                - cuffcompare部分可以对两个或多个样本的GTF文件进行比较，也可以用自己整合后的完整转录本与已知的基因注释进行比较来寻找新的转录本；
                - cuffquant部分：可以对单个BAM文件的基因转录本表达水平进行定量分析；
                - cuffnorm部分：用cuffquant的输出文件（.cxb）或者cufflinks的输出文件（.bam）作为输入文件，对所有样本表达水平进行统一的标准化去除数据来源的偏倚，并把标准化后的表达值整合在一个文件中。
            - 安装一致报错，而且要sudo权限貌似，暂时就先用/gss1/env下的学校制作的环境（source一下对应的.env文件即可，退出到其他目录也行）；
        - 简单用法
            - cufflinks主要命令：`cufflinks [options] <aligned_reads.(sam/bam)>`
                - `<aligned_reads.(sam/bam)>`：cufflinks需要SAM或BAM格式的文本文件作为输入文件，并且必须经过排序（比如tophat2产生的已经经过排序的bam文件），针对未排序的sam或bam文件可以使用`sort -k 3,3 -k 4,4n hits.sam > hits.sam.sorted`进行排序。
                - -p:线程数；
                - -G|--GTF:基因组注释文件；提供一个GFF文件，以此来计算isoform的表达。此时，将不会组装新的transcripts，程序会忽略和reference transcript不兼容的比对结果;
                - -g|--GTF-guide：提供GFF文件，以此来指导转录子组装(RABT assembly)。此时，输出结果会包含reference transcripts和novel genes and isforms，也就是相对-G参数只输出注释到的转录本的表达值；
                - --library-type：默认值fr-unstranded，这是Illumina TruSeq的文库类型。
                - -o {outout_folder}：输出文件的目录；
                - -u：让cufflinks来做initial estimation步骤，从而更精确衡量比对到genome多个位点的reads；
                - -L|--label：默认值是"CUFF"，Cufflink以GTF格式来报告转录子片段(transfrags),该参数是GTF文件的前缀
                - -M|--mask-file：提供一个GFF或GTF文件。Cufflinks将忽略比对到该GTF文件的transcripts中的reads。该文件中常常是rRNA的注释，也可以包含线立体和其它希望忽略的transcripts的注释。将这些不需要的RNA去除后，对计算mRNA的表达量是有利的。
            - cufflinks输出的结果：
                - transcripts.gtf：包含Cufflinks的组装结果，前7列为标准的GTF格式，最后一列为attributes；
                - ispforms.fpkm_tracking：isoforms(可以理解为gene的各个外显子)的fpkm计算结果；
                - genes.fpkm_tracking：gene的fpkm计算结果；
            - cuffcompare主要命令：`cuffcompare -o {cuffcmp} cuff1.gtf cuff2.gtf ...`
                - 好像是用来比较你用cufflinks组装出来的转录本与参考注释的结果进行比较，判断与已知的转录本是否匹配，还是此转录本为新的转录本。
                - -o {outprefix}：输出文件的前缀；默认是cuffcmp；
                - {cuff1.gtf}：由cufflinks产生的gtf文件；
                - -r {gtf|gff}：参考注释文件；
                - 结果文件：
                    - 其中的combined.gtf包含了在所有样本中都有的转录本；
            - cuffmerge主要命令：`cuffmerge -o ./merged_sam -g gtf -p 8 {assembly_list.txt}`
                - 可以将各个Cufflinks生成的transcripts.gtf文件融合称为一个更加全面的transcripts注释结果文件merged.gtf。这一步的工作就是方便后面用Cuffdiff来分析基因差异表达。 
                - -o {outprefix}：文件输出目录，整合的gtf为{outprefix}/merged.gtf；
                - -p {int}：线程数；
                - -g|--ref-gtf：参考注释；
                - {assembly_list.txt}：需要先将所有需要整合的GTF文件所在的路径保存在assemblies.txt文件中。另外使用-g参数还可以在整合时加入额外的参考注释GTF文件，将已知的转录本和新的转录本融合在一起。
            - cuffquant主要命令：`cuffquant -o ./ -p 6 -b Ref_Genome/$idx_prefix.fa -u --max-bundle-frags 50000000 --library-type fr-unstranded {merged.gtf} {$sample/accepted_hits.bam}`
                - cuffquant是cuffquant能够对单个 BAM 文件的基因转录本表达水平进行定量分析。生成的是CXB文件abundances.cxb，可以作为cuffdiff的输入，**这会加快cuffdiff的运行速度**。也可以作为Cuffnorm的输入。
                - -o：文件输出目录；输出的是一个二进制的abundances.cxb文件，可以用于cuffdiff和cuffnorm；
                - -p：线程数；
                - -b|--frag-bias-correct {genome.fa}：有文章说这个是`samtools faidx Ref_Genome/Genome.fasta`构建的基因组索引，但是这个faidx的结果不是fai后缀的吗，也不是fasta格式啊，暂时就不写吧；
                - -u：可以提高精度；
                - --max-bundle-frags：一个位点被所具有的片段的最大数量，超过这个数量将被剔除；默认是1000000；有教程设置为50000000；但是riceENCODE的pipeline直接默认，并且上面的-b参数也没有写；
                - --library-type：默认值fr-unstranded，这是Illumina TruSeq的文库类型；
                - {merged.gtf}：Cuffmerge的merged.gtf结果，不过应该也可以单个样本的transcripts.gtf；
                - {$sample/accepted_hits.bam}：可以是sam的，也可以是bam格式的；
            - cuffdiff主要命令：`cuffdiff -o diff_out -b Ref_Genome/$idx_prefix.fa -p 8 –L C1,C2 -u {transcripts.gtf} {sample1_replicate1.sam[,...,sample1_replicateM.sam]} {sample2_replicate1.sam[,...,sample2_replicateM.sam]}... [sampleN.sam_replicate1.sam[,...,sample2_replicateM.sam]]`
                - cuffdiff用来寻找差异表达的基因（转录本）；
                - -o：文件输出目录；
                - -b：同cuffquant的-b参数；
                - -p：线程数；
                - -u：使结果更加准确的参数；：
                - -L|--lables {C1,C2}；给每个sample一个样品名或者一个环境条件一个lable；default: q1,q2,...qN；
                - {transcripts.gtf}：之前的cuffmerge的结果merge.gtf，当然也支持单个样本的gtf文件，或者其他来源的；
                - --library-type：类似之前，默认就是fr-unstranded，可以不写；
                - --no-update-check：不检查cufflinks是否是最新版；
                - -T|--time-series：让Cuffdiff来按样品顺序来比对样品，而不是对所有的samples都进行两两比对。即第二个SAM和第一个SAM比；第三个SAM和第二个SAM比；第四个SAM和第三个SAM比...
                - `{sample1_replicate1.sam[,...,sample1_replicateM.sam]} {sample2_replicate1.sam[,...,sample2_replicateM.sam]}... [sampleN.sam_replicate1.sam[,...,sample2_replicateM.sam]]`：所有样本及其重复的bam或sam文件（或者也可以是cuffquant的结果文件CXB files，但请所有样本保持一致），比如有三个样本C1、C2、C3，每个样本2个重复R1、R2，则可以写成`C1_R1.sam,C1_R2.bam,C2_R1.bam C2_R2.bam,C3_R1.bam,C3_R2.bam`，样本重复数可以不一样；
                - cuffdiff默认比较两两对照之间的关系，如果想要比较特定组别之间的关系，可以指定一个tab分隔的contrast.txt文件，然后用-C {contrasts.txt}参数，该文件首行为列名，如：
                    ```
                    condition_A	condition_B
                    Ctrl	Mutant_X
                    Ctrl	Mutant_Y
                    Ctrl	Mutant_Z
                    ```  
                - 示例（来自软件的pipeline-paper，不过应该是version1）：
                    ```
                    cuffdiff -o diff_out -b {genome.fa} -p 8 –L C1,C2 -u merged_asm/merged.gtf \ 
                    ./C1_R1_thout/accepted_hits.bam,./C1_R2_thout/accepted_hits.bam,./C1_R3_thout/ accepted_hits.bam \ 
                    ./C2_R1_thout/accepted_hits.bam,./C2_R3_thout/accepted_hits.bam,./C2_R2_thout/ accepted_hits.bam
                    ```
            - cuffdiff结果
                1. FPKM tracking files：
                    - 基因或CDS或transcript的在每个样本中的FPKM值，包括isoforms、genes、cds、tss_groups为前缀的tracking_file的文件，分别代表转录本、基因、编码序列和Primary transcript（初级转录本，纲转录完的DNA的拷贝）的FPKM；
                2. Count tracking files：
                    - 类似上面的FPKM tracking files，只不过计算的是counts数而不是FPKM值，同样有四个文件；
                3. Read group tracking files
                    - 展现各个样本的每个重复的FPKM值，和上面一样也包含四个文件；
                4. Differential expression tests
                    - 针对转录本或基因或cds在两两样本间的差异表达检验信息，包含log2(fold_change)、显著值等信息；同样包括四个以`_exp.diff`结尾的文件；
                    - 这是一个相对来说比较重要的文件，可以用于筛选差异基因。以gene_exp.diff文件为例，分别包含Tested id、gene、locus、sample 1、sample 2、Test status（可以是OK，表示通过检验，可以是NOTEST，表示没有足够的alignment去检验；还可以是LOWDATA、HIDATA和FAIL）、FPKMx、FPKMy、log2(FPKMy/FPKMx)、test stat、p value、q value（FDR-adjusted p-value）、significant。
                5. Differential splicing tests - splicing.diff
                    - 不同isoform之间的可变剪切检验，只有多个isoforms的primary transcripts才有这些信息；一般没用吧，可能在研究可变剪切中需要用到；
                6. Differential coding output - cds.diff
                    - 同上；
                7. Differential promoter use - promoters.diff
                    - 探究样本间的启动子使用频率的差异，只有基因有两个或多个primary transcripts才有此信息；
                8. 其它文件：
                    - `Read group info - read_groups.info`陈列了使用该软件输入的样本文件的基因信息；
                    - `Run info - run.info`展示软件运行的参数和软件信息；
            - cuffnorm主要命令：`cuffnorm -p 6 -L --library-type fr-unstranded --output-format cuffdiff -o ./Cuffnorm <transcripts.gtf> <sample1_replicate1.sam[,...,sample1_replicateM.sam]> <sample2_replicate1.sam[,...,sample2_replicateM.sam]>... [sampleN.sam_replicate1.sam[,...,sample2_replicateM.sam]]`
                - 用于对表达值进行标准化，和cuffdiff的输入输出有点类似，不过它包含的是标准化后的fragment counts；
                - 有和cuffdiff一样的-o、-L、-p、--library-type、--no-update-check参数，但是没有-b、-u等参数；
                - cuffnorm不像cuffdiff进行差异表达分析；它的输出结果的格式和cuffdiff不一样，如果想要和cuffdiff一致，可以使用`--output-format cuffdiff`参数来指定输出结果；cuffnorm；它输出的标准化后的raw counts，所以后面想要进行下游的差异表达工具，必须是要能接受raw counts作为输入的工具；（DeSeq2需要的是未标准化的）
                - cuffdiff和cuffnorm都支持一个--use-sample-sheet参数，可以用来声明你的样本的label和对应的sam或bam文件的对应关系，感觉作用不大；
            - cuffnorm结果文件
                1. Simple-table expression format：
                    - 包含cds、genes、isoforms、tss_groups前缀的*.fpkm_table files and *.count_table files，第一列为id，第二列为`The FPKM value (for *.fpkm_table files) or normalized fragment count (for *.count_table files) for this feature in replicate N of conditionX`；
                2. Simple-table gene attributes format
                    - 以attr_table结尾、包含对应的基因或转录本或cds的metadata；
                3. Simple-table sample attributes format
                    - 名为samples.table文件，包含样本的一些信息，无关紧要；
            - cuffdiff和cuffnorm的结果文件比较
                - cuffdiff的genes.count_tracking文件和cuffnorm的genes.count_table文件
                    - 图片待插入 
                    - 文件第一列两者都一样是tracking_id，但是genes.count_tracking对每一个样本的count值都包含四列，包括count、count_variance、count_uncertainty、status；而genes.count_table文件对于每一个样本的每一个重复的count都有数值，也就是说genes.count_tracking样本count值是将genes.count_table中每个样本的两个重复的count取均值得到的；
                    - 这个counts是否可用呢？
                        - 图片待插入 
                        - 官方文档里表明这个不是raw counts，同样有回答表明Bowtie-Tophat-Cufflinks流程不会产生raw counts：
                            - [Raw read count on Cuffdiff](https://www.biostars.org/p/136378/)
                            - [Cufflinks bias correction and DESEq2](https://www.biostars.org/p/175665/)
                - genes.fpkm_table和genes.fpkm_tracking
                    - 图片待插入
                - 能否从bowtie2+tophat2+cufflinks流程中获取到样本的raw counts？还有我自己提问的链接
                    - 根据fpkm的计算公式
                        - [Why cufflinks does not report raw read count anymore? Which version of it do report?](https://www.biostars.org/p/138084/#138216) ：不建议recompute the read counts from the FPKM values，since cufflinks does some length normalisations of the transcripts as well as assigning multi-mapping reads.，建议使用HTseq，也有建议使用featureCounts的； 
                    - 使用htseq-count或者featureCounts计算raw reads
                        - [Cufflink, where is the raw gene count?（biostars）](https://www.biostars.org/p/271203/)、[How do you extract raw counts from CUFFLINKS?](https://www.biostars.org/p/107635/)、[Raw read count on Cuffdiff](https://www.biostars.org/p/136378/)、[Raw Counts From Cufflinks Output](https://www.biostars.org/p/63590/)、[Cufflinks bias correction and DESEq2](https://www.biostars.org/p/175665/):：都建议用HTseq或featureCounts进行计算得到raw counts
                        - https://www.seqanswers.com/forum/bioinformatics/bioinformatics-aa/4868-converting-fpkm-from-cufflinks-to-raw-counts-for-deseq 
    - <a href="https://imgse.com/i/pEn8zgf"><img src="https://s21.ax1x.com/2025/02/10/pEn8zgf.png" alt="pEn8zgf.png" border="0"></a>
   -  
    1. 基因表达定量
        - 使用HTSeq进行表达定量
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
                - 主要命令：`htseq-count -f bam -r name -s no -a 10 -t exon -i gene_id -m intersection-nonempty yourfile_name.bam ~/reference/hisat2_reference/Homo_sapiens.GRCh38.86.chr_patch_hapl_scaff.gtf > counts.txt`
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
                        3. 使用infer_experiment.py程序
                            - 主要命令：`infer_experiment.py -i ../4_mapping_tophat/result_SRR10751892/accepted_hits.bam -r Oryza_sativa_mh63.MH63RS2.60.bed -q 20`
                                - -i 比对生成的bam文件（可以不用排序）
                                - -r gtf转bed12文件产生的bed文件。
                                - -s 从所有的reads中抽取多少进行统计（默认200k）
                                - -q unique map的mapq阈值
                        4. 结果解读
                            - 如果两个数值接近1:1，则为非链特异性，如果比例悬殊则为链特异性；“1+-，1-+，2++，2--”这，也就是read1在+链，相对的gene其实是在-链（reverse）。这种就是“fr-firststrand”相比于fr-secondstrand现在更为常见。
                            - 图片待插入  
    2. 表达矩阵的标准化/normalization
        - <a href="https://imgse.com/i/pEnGPbQ"><img src="https://s21.ax1x.com/2025/02/10/pEnGPbQ.png" alt="pEnGPbQ.png" border="0"></a>
        - <a href="https://imgse.com/i/pEnDw4O"><img src="https://s21.ax1x.com/2025/02/10/pEnDw4O.png" alt="pEnDw4O.png" border="0"></a>
        - 根据htseq的结果count.txt生成可以用于DESeq2的表达矩阵文件
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
    3. 质控
        - 样本过滤和基因过滤
5. PCA和样本相关性分析
   - <a href="https://imgse.com/i/pEnGA5n"><img src="https://s21.ax1x.com/2025/02/10/pEnGA5n.png" alt="pEnGA5n.png" border="0"></a>
   - <a href="https://imgse.com/i/pEnGFEj"><img src="https://s21.ax1x.com/2025/02/10/pEnGFEj.png" alt="pEnGFEj.png" border="0"></a>
6. 差异表达分析
    1. 准备工作
       - 软件：DESeq2
    2. 筛选差异表达基因
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
        - 使用CummeRbund R包进行cuffdiff软件后续的分析
            - 教程（还有官方的那篇paper）：
                - [cummeRbund:用于Cufflinks高通量测序数据的可视化软件包（基本没用）-2017-09](https://mp.weixin.qq.com/s/m4sJE5B_O00ltab89ZV2Uw)
                - [Pipeline RNA-seq-github(2017)](https://github.com/johanzi/RNA-seq_pipeline?tab=readme-ov-file#cufflinks-pipeline)
                - [bioconductor](https://www.bioconductor.org/packages/release/bioc/html/cummeRbund.html)
            - 安装与简介
                - 是cuffdiff做RNA-seq的下游工具；CummeRbund创建了一个SQLite数据库，将cuffdiff运行产生的结果都存储到数据库中，将genes、transcripts、transcriptionstart sites、以及CDS建立关联。将这些数据存储到数据库中，并建立相关的索引，就可以很容易的对多个样本之间或者其他条件的数据进行查询检索，允许用户对于单个或者一组基因的各种feature进行比较分析。同时还提供了诸多的绘图函数，可以满足一般的数据可视化需要。
                - 使用`BiocManager::install("cummeRbund")`进行安装；
            - 使用方法
                1. 使用readCufflinks函数读取目录下的cuffdiff结果：`cuffdiff_data <- readCufflinks('/dir/to/cuffdir_output_dir/')`
                2. 筛选差异基因
                    ```
                    # Check for diff expressed genes
                    gene_diff <- diffData(genes(cuffdiff_data))

                    # Get DEGs based on default p-value threshold (5%)
                    sig_gene_diff <- subset(gene_diff, (significant == 'yes'))

                    # Get genes with significant differential expression based on chosen p-value threshold (1%)
                    sig_gene_diff <- subset(gene_diff, (q_value < 0.01))

                    # Get subset of genes based on fold-change (in this case, 4-fold change)
                    sig_gene_diff <- subset(gene_diff, ( abs(log2_fold_change) > 2 & significant == 'yes'))
                    ```
                3. Extract information about DEGs
                    ```
                    # Check number of DEGs
                    nrow(sig_gene_diff)

                    # Get names of the DEGs
                    name_sig_gene_diff =  getGenes(cuffdiff_data, sig_gene_diff$gene_id)
                    list_genes = featureNames(name_sig_gene_diff)

                    # Export table with names of the genes
                    write.table(list_genes[2], 'list_genes.txt', sep='\t',row.names = F, col.names = T, quote = F)
                    ```
                4. Create a heat-map of the genes
                    ```
                    csHeatmap(sig_gene_diff, cluster='both')

                    # Export as pdf
                    pdf("sig_gene_diff.pdf", family="Helvetica", width=7, height=7)
                    csHeatmap(name_genes_mpi, cluster='both')
                    dev.off()
                    ```
                5. Compare the expression of each gene in two conditions with a scatter plot
                    ```
                    csScatter(genes(cuffdiff_data), 'tissue1', 'tissue2')

                    # Scatterplot only for DEGs
                    csScatter(sig_gene_diff, 'tissue1', 'tissue2') 
                    ``` 
                6. Make a volcano plot with a red highlight on significant DEGs (p-value<5%)
                    ```
                    csVolcanoMatrix(genes(cuffdiff_data))
                    ```
                7. Get density plot showing the distribution of the RNA-seq read counts (fpkm)
                    ```
                    # All genes
                    csDensity(genes(cuffdiff_data))

                    # Only DEGs
                    csDensity(sig_gene_diff)
                    ```
                8. Get expression plots of a set of genes of interest
                    ```
                    gene_of_interest <- getGene(cuffdiff_data,'name_genes')
                    expressionBarplot(gene_of_interest)

                    # Use a vector if multiple genes
                    genes_of_interest_IDs <- c("gene1","gene2","gene3")
                    genes_of_interest <- getGene(cuffdiff_data, genes_of_interest_IDs)
                    expressionBarplot(genes_of_interest)
                    ```
                9. Check clustering of biological replicates
                    `csDendro(genes(cuffdiff_data), replicates=T)` 
    3. 基因名称注释
    4. 差异表达作图
    - <a href="https://imgse.com/i/pEnGkUs"><img src="https://s21.ax1x.com/2025/02/10/pEnGkUs.png" alt="pEnGkUs.png" border="0"></a>
    - <a href="https://imgse.com/i/pEnBQld"><img src="https://s21.ax1x.com/2025/02/10/pEnBQld.png" alt="pEnBQld.png" border="0" /></a>
    - <a href="https://imgse.com/i/pEnGCDg"><img src="https://s21.ax1x.com/2025/02/10/pEnGCDg.png" alt="pEnGCDg.png" border="0"></a>
7. 富集分析
    - 教程
        - [常规富集分析的原理解析](https://mp.weixin.qq.com/s/DqP0Qfpd0VZaJzje8XOQYA)
        - [KEGG富集分析](https://www.bilibili.com/video/BV15MR8YHEM9/?spm_id_from=333.337.search-card.all.click&vd_source=2523c7055f0985a7f47ca59739b6b086)
    - 富集分析背景知识
        - 在RNA-seq中，拿到差异基因后，通常希望想要知道这些能够表征样本间差异的基因都参与了什么通路？哪些通路是重要的？从而推出样本间重要的功能差异，将研究上升到功能机制层面且利于后续机制实验的开展。“功能富集分析”可理解为在差异基因集中寻找占比高的功能，从而了解差异基因集的关键功能，进而推出样本间重要的功能差异。
        - 富集分析过程
            1. 选择基因注释数据库
                - gene function是一个十分广泛的概念，不同的研究者有不同的定义和关注点，不同数据库也基于不同的分类思想对基因进行归类：
                    - GO数据库分别从分子功能（Molecular Function，MF）、细胞组分（Cell Component，CC）和生物学过程（Biological Process，BP）三个层面对基因的功能进行注释；KEGG数据库、Reactome Pathway数据库、WikiPathways数据库对基因参与的通路进行注释；DO数据库对基因参与的人类疾病进行注释；MSigDB数据库对人和小鼠的基因进行了多角度的全面注释。
                - 因此，首先我们需要根据研究目的，选择合适的数据库对差异基因进行功能归类，并统计每一条目的数据如表：
                    - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250310153253.png"/>
            2. 统计分析
                - 如何定义富集？
                    - “富集”指的是一个给定的生物学过程、通路或功能在前景基因集中出现的频率显著高于其在背景基因集中出现的频率，即：若m/n > M/N，认为条目A在前景基因集中富集。这就像从一大筐球中摸出n个球，若得到A类球的比例大于原先筐中A类球的比例，则认为A类球在摸出的n个球中富集，因此常规的富集分析问题符合超几何分布。
                - 如何判断这种富集不是偶然的（富集显著）？
                    - 换句话说，条目A的富集是否是偶然的？这需要进行超几何检验来计算显著性。计算从M个背景基因中随机选择n个基因，其中至少有m个基因属于条目A的概率：
                        - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/145d0b650ff74cb6b35e1fba9b89e180.png"/>
                    - 若p-value≤0.05，说明从背景基因中随机选择n个基因，至少有m个基因属于条目A的概率极小，但是现实情况却发生了，则认为条目A显著富集。由于数据库中包含上百个条目，每个条目都要做一次检验，每次检验都有5%的概率是假阳性，随着检验次数增多，假阳性结果出现的次数就会增多，为了控制阳性结果中的错误率（False Discovery Rate，FDR），需要进行多重假设检验校正。
                    - 举个例子，对300条通路进行了300次检验，即使他们实际上都不富集，也会出现15条通路富集的结果。若对300条通路进行300次检验，得到30条通路富集，其中有15条是假阳性结果，那么FDR=15/30=0.5，也就是说你获得的结果有一半是不可信的，因此要对p-value进行校正来将FDR控制在一定范围内。最常用的是BH（Benjaminiand Hochberg）法校正，最终我们应该使用校正后的p值（p.adjust）来筛选富集的条目。  
                - 如何比较两个条目的富集程度？
                    - 富集程度可以通过富集因子（rich factor）即m/M，注释到此条目上的差异基因数m和p.adjust这三个指标来衡量。一般情况下。我们直观地认为m越大，该条目越重要，但就像人均GDP比GDP更能代表生活水平一样，研究者提出了对条目大小进行标准化的富集因子作为衡量指标之一。
                    - 以上描述的就是常规的GO和KEGG富集分析所采用的过度代表性分析（Over Representation Analysis，ORA）原理。总结为首先使用特定阈值创建输入列表（差异基因集），然后对输入列表进行功能归类和条目计数，最后采用超几何检验和多重假设检验校正计算富集的显著性。 
    1. KEGG富集
        - 使用实例
            - 先准备一个id.txt文件，参考如下：
                - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250310160703.png"/>
            - 代码：
            ```
            library("clusterProfiler")
            library("org.Hs.eg.db") # 人的基因ID转换包
            library("enrichplot")
            library("ggplot2")
            setwd("your_path")
            rt=read.table("id.txt", sep="\t", header=T, check.names=F)
            rt=rt[is.na(rt[,"entrezID"])==F,] # 剔除NA值
            gene=rt$entrezID
            # kegg富集分析
            kk <- enrichKEGG(gene=gene，organism= "hsa"，pvalueCutoff = 0.05，qvalueCutoff = 0.05)
            write.table(kk, file="KEGG.txt", sep="\t", quote=F, row.names=F)
            # 柱状图
            pdf(file="barplot.pdf", width=10, height=7)
            barplot(kk, drop=TRUE, showCategory= 30)
            dev.off()
            # 气泡图
            pdf(file="bubble.pdf", width=10, height= 7)
            dotplot(kk.showCategory=30)
            dev.off()
            ```  
    2. GO富集
    3. reactome富集
## 四、RNA-seq实验设计
### 相关问题
1. 转录组分析是测序深度重要还是生物学重复重要？
    - 生物学重复对实验结果影响要大得多；
2. 转录组分析多少生物学重复合适？重复少会出现什么直接后果？
    - 一般大于6个重复，重复越少，假阴性率越高，你筛选到的差异表达基因越少；但情况并没有你想象的那么糟糕，你筛选到的基因还是值得相信的。
3. 转录组分析重复不足（n<6）时，会有哪些后果，我们该怎么做？
    - （1）如果筛选的差异基因很少，当你的实验设计多于两个condition时，就会产生一定的问题。
    - （2）用更严格的分析方法如，DESeq、edgeR、sleuth等；
    - （3）差异倍数较大的基因（FC>4）被遗漏的风险较小。 
### 补充知识
1. IGV使用
    - 教程
        - [](https://mp.weixin.qq.com/s/mRE65voGQTW-wVs5H43n2w)
        - [](https://mp.weixin.qq.com/s/znEaMZqfrLAc6vAsqtbBzw)
    - 安装与简介
        - IGV是一个将基因组学数据可视化的可交互工具，可用于展示和查看基因表达、突变、融合、甲基化、探针等多种信息。
        - [IGV 安装](https://mp.weixin.qq.com/s/JUcjo1BCyZc2dnsYw4aXTA)
    - 使用IGV查看下机数据比对到基因组的bam文件
        1. 导入参考基因组文件：点击Genomes进行load，导入成功后，会生成一个.fai的文件；
        2. 导入bam文件
            - 这里使用到的bam文件必须是排过序的；`*.bam`所在文件夹下要有对应的`*.bai`文件，可以使用samtools工具或者IGV中的igvtools工具生成bai文件；在这里我们使用igvtools生成bai文件： 
                - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250306104117.png"/>
            - 一个bam文件加载后，会有三个tracks：coverage、junction（剪接）和比对情况：
                - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250306105950.png"/>
                - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250306110654.png"/>
                - 【覆盖度】tracks，展示reads在基因组每个位置的分布情况，默认灰色。当某变异位点的碱基类型与参考序列不一致超过20%时，会根据碱基类型和数目进行着色；一般来说，几乎都是灰色的；
                - 【剪接连接】tracks，展示junction情况和正负链分布情况。红色为比对到正链，蓝色为比对到负链。
                - 【比对情况】tracks，展示变异情况。如纯合或杂合突变、indel等；IGV 表示相对于参考基因组的插入，使用紫色表示插入；IGV 用黑色线条表示相对于参考基因组的缺失；
        3. 同时导入多个测序文件(bam文件)进行展示
            - 可以点击左侧，将所有样本选择只显示coverage，然后拖动样本放在一起，使它们的coverage tracks紧挨在一起；
2. 基因ID转换
    - 教程
        - [关于基因ID转换，一文就够了](https://mp.weixin.qq.com/s/S7sf2okHP0cf7_SiV7SjMg)
    - 常见的基因iD
        - gene symbol或者说gene name：Gene symbol也分为官方的基因名和亚名，通俗点来说，就是大名（Official）和小名（亚名）。Official gene symbol可以做基因ID转换和富集分析，以及多个数据集的整合分析，基因亚名可以让年纪大一点/不懂生信的审稿人认出他们熟悉的基因名字，比如提到PTPRC，一般人不知道这个是CD45。
        - Gene ID，即Entrez ID：指的是来自于NCBI旗下的Entrez gene数据库所使用的编号，其实就是来自于NCBI里面的gene数据库。每个基因的编号具有唯一性，包括不同种属生物间的同源基因编号也不相同。
        - Ensemble ID，相对于NCBI的Entrez ID，Ensembl是另外一个记录基因信息的数据库。不管是什么类型的，Ensemble ID的前三个开头都是以ENS开头的（ENSG是基因，ENST是转录本）；
        - 除此之外，还有NCBI的refseq ID，常用于在NCBI数据库获取基因序列，比如引物设计，质粒构建。
    - 同一种属的基因ID转换
        1. 如果是人、小鼠等比较常见的物种（一般都是动物，没有水稻等职务）的基因ID的转换，可以使用类似于`org.Hs.eg.db`（人的）的一系列包进行转换，里面内置了对应物种的各种ID；
            - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250307163003.png"/>
            - 参考代码
                    ```
                    data = openxlsx::read.xlsx("./GSE10072_expression_clinical_data_1.xlsx",
                              sheet = 1,rowNames = T)
                    ## 加载R包
                    library(clusterProfiler)
                    library(org.Hs.eg.db) #for Human
                    library(org.Mm.eg.db) #for Mouse
                    keytypes(org.Hs.eg.db)
                    keytypes(org.Mm.eg.db)
                    gene.df <- bitr(row.names(data), fromType = "SYMBOL", # 从SYMBOL到ENSEMBL和ENTREZID,得到新命名的gene.df
                                   toType = c("ENSEMBL","ENTREZID"),
                                   OrgDb = org.Hs.eg.db) #小鼠换成org.Mm.eg.db
                    head(gene.df)
                    ``` 
        2. 使用biomaRt包
            - biomaRt包是从网页上加载注释的，网络不好的话比较慢，可以先将常使用的物种保存下来，每次使用的时候load一下即可。
            - 参考代码：
                ```
                    ## 加载R包
                    library(clusterProfiler)
                    library(biomaRt)

                    human = useMart("ensembl", dataset = "hsapiens_gene_ensembl")
                    mouse = useMart("ensembl", dataset = "mmusculus_gene_ensembl")
                    human_list = listFilters(human)
                    mouse_list = listFilters(mouse)
                    save(human,mouse,file = "./biomaRt_ensembl_annotation.Rdata")
                    rm(list())
                    load(file = "./biomaRt_ensembl_annotation.Rdata")
                    head(human_list)
                    head(mouse_list)

                    ## 开始进行ID转换
                    data = openxlsx::read.xlsx("./GSE10072_expression_clinical_data_1.xlsx",sheet = 1,rowNames = T)
                    gene.df <- bitr(row.names(data), fromType = "hgnc_symbol",
                                   toType = c("ensembl_gene_id","entrezgene_id"),
                                   OrgDb = human) #小鼠换成mouse
                    head(gene.df)
                ```
            - 
    - 不同种属的基因ID转换
        - 使用biomaRt包
            - 参考代码
                ```
                    ## 加载储存好的注释文件
                    load(file = "./biomaRt_ensembl_annotation.Rdata")

                    # Basic function to convert mouse to human gene names
                    convertMouseGeneList <- function(x = x){
                     require("biomaRt")
                     #human = useMart("ensembl", dataset = "hsapiens_gene_ensembl")
                     #mouse = useMart("ensembl", dataset = "mmusculus_gene_ensembl")
                     genesV2 = getLDS(attributes = c("mgi_symbol"), filters = "mgi_symbol", values = x , mart = mouse, attributesL = c("hgnc_symbol"), martL = human, uniqueRows=T)
                     humanx <- unique(genesV2[, 2])
                     # Print the first 6 genes found to the screen
                     print(head(humanx))
                     return(humanx)
                    }

                    # Basic function to convert human to mouse gene names
                    convertHumanGeneList <- function(x){
                     require("biomaRt")
                     #human = useMart("ensembl", dataset = "hsapiens_gene_ensembl")
                     #mouse = useMart("ensembl", dataset = "mmusculus_gene_ensembl")
                     genesV2 = getLDS(attributes = c("hgnc_symbol"), filters = "hgnc_symbol", values = x , mart = human, attributesL = c("mgi_symbol"), martL = mouse, uniqueRows=T)
                     humanx <- unique(genesV2[, 2])
                     # Print the first 6 genes found to the screen
                     print(head(humanx))
                     return(humanx)
                    }

                    ## 例如，Human转mouse
                    data = openxlsx::read.xlsx("./GSE10072_expression_clinical_data_1.xlsx",
                                              sheet = 1,rowNames = T)
                    mus_genes <- convertHumanGeneList(row.names(data))
                ```
        - 一些相应物种的数据库可以提供ID转换
            - RIGW数据库，[Rice Information GateWay](http://rice.hzau.edu.cn/rice_rs3/)
                - 使用ZS97和MH63为参考基因组，能提供ZS97、MH63、9311和日本晴之间的直系同源基因ID的转换，并提供KEGG和GO富集工具。
                - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250307170533.png"/>
    - 使用biomaRt包进行基因ID注释
        - 教程：
            - [R语言biomaRt包基础使用方法](https://mp.weixin.qq.com/s/7vvxZomAYidrGXkfqQ4Zwg) 
        - 安装与简介
            - 使用BiocManager安装；
            - biomaRt包可以轻松获取Ensembl上的数据，可以在各种基因名和不同的基因ID之间进行转换，也可以根据基因的ID获取基因的序列，还可以获取GO注释，SNPs的信息；
        -  基本使用：
            1. 选择你需要的database：
                - 这一步就是选择BioMart要链接到的数据库； 
                - BioMart database与Ensembl database的关系：
                    - BioMart是一个更广泛的数据查询平台/工具，它可以访问多个生物学数据库，如Ensembl、Uniprot、WormBase；而Ensembl只是其中一个数据库，仅包含 Ensembl 提供的基因、变异、调控数据；
                    - useEnsembl()直接连接到 Ensembl BioMart 数据库，只适用于Ensembl database，用户不需要手动指定 URL，相关函数有`listEnsembl() listEnsemblGenomes()`；而useMart()可以连接 BioMart 平台中的任意数据库，适用于所有 BioMart 提供的数据库，可以使用`listMarts()`进行所支持的数据库列表查询；
                    - 代码展示：
                        ```
                            # 连接 Ensembl 基因数据库
                            ensembl <- useEnsembl(biomart = "genes", dataset = "hsapiens_gene_ensembl")

                            # 查看可用数据集
                            listDatasets(ensembl)
                        ```
                        ```
                            # 列出 BioMart 平台所有可用的数据库
                            listMarts()

                            # 连接 Ensembl BioMart
                            ensembl <- useMart("ENSEMBL_MART_ENSEMBL")

                            # 查看可用数据集
                            listDatasets(ensembl)
                        ```
                - 
            2. 选择dataset/指定物种
                - 每个物种就是一个dataset，`listDatasets()`可以展示有哪些dataset是可选的，但你可能很难从中找到你需要的。`searchDatasets()`提供了一个搜索功能，你可以检索你需要的物种，这里以人种为例，搜索hsapiens；
                - 代码示例：
                    ```
                        datasets <- listDatasets(ensembl)
                        head(datasets)
                        searchDatasets(mart = ensembl, pattern = "hsapiens")
                    ```
                - 知道要用的dataset后，接下来使用useDataset()来更新Mart对象：`ensembl <- useDataset(dataset = "hsapiens_gene_ensembl", mart = ensembl)`
            3. 选择镜像
                - 可以选择离你最近的镜像地址来加速数据的获取，镜像包括：useast, uswest, asia, www：`ensembl <- useEnsembl(biomart = "ensembl", dataset = "hsapiens_gene_ensembl", mirror = "useast")`
            4. 选择Ensembl的版本
                - listEnsemblArchives()可以显示可选的Ensembl版本;
                - 代码示例：
                    ```
                        listEnsemblArchives()
                        listEnsembl(version = 95) # 选择指定的Ensembl版本
                        ensembl95 <- useEnsembl(biomart = 'genes', dataset = 'hsapiens_gene_ensembl', version = 95)
                    ```
            5. 使用getBM函数进行检索(根据attributes值的选择完成ID转换)
                - 上面相当于构建了一个数据库，里面包含了所有的数据，下面需要制定你的检索条件，该函数主要包含三个参数：
                    - filters和values这两个参数定义你检索的条件，比如想检索位于X染色体上所有的基因，则你的filters为"chromosome_name",value参数为"X"
                    - attributes：指定返回的结果都包含哪些信息，比如entrezgene_id；可以使用`attributes = listAttributes(ensembl);attributes[1:5,]`查看可以获取的Attributes。
