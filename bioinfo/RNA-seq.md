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
### 3.2 具体分析流程
1. 原始数据上传
    - 有时是自己的数据，就需要本地上传到服务器，这时候需要注意传输过程中的数据完整性； 
    - 使用md5值进行检验数据完整性：
        - 给自己的文件生成md5值：`md5sum *gz > md5.txt`(一个文件一个值)
        - 比对已有的md5值（就是传输之前要生成一次md5值，用来在传输之后进行比对）：`md5sum -c md5.txt`
2. 原始数据下载（如果不是直接得到的话）
    1. 相关知识
        1. [GEO和SRA数据库](https://phantom-aria.github.io/2022/05/04/a.html)
           - GEO数据库是基因表达数据库，一般提供的都是中间文件，比如转录组数据的表达矩阵，芯片数据的中间注释文件，单细胞测序的细胞注释，突变的vcf突变文件等等。部分文章只提供SRA。（点击`SRA run selector`进入样本下载界面） 
           - SRA数据库，存储原始测序数据的数据库
        2. GEO数据库的GPL（platform）、GSM（Sample）、GSE（Series）
           - 文章提供的是NCBI的GSE number：比如练习数据GSE142570；点击链接进入后可以看到以GPL、GSE、GSM开头的编号；其中GPL表示测序用的平台、GSE代表一系列相关样本组成的数据集、GSM代表单个样本的数据记录，还包含该样本的的详细元数据（如实验条件、处理方式、生物学重复等），一个GSM可以是单独的样本，也可以是多样本的实验组。也就是说GSE和GSM是一对多的关系，每个GSE包含多个GSM。
           - 比如练习数据有513个Sample（但是文中说的是510个数据），也就是对应了513个GSM编号；共有ChIP-seq（355 samples）、RNA-seq（58）、FAIRE-seq（58）、BiSulfite-seq（38）、ChIP-reChIP-seq（4）5个数据集，即对应5个GSE编号，共513个样本。
        3. SRA数据库的SRP（Project）、SRS(样本 Sample)、SRX(数据产生 Experiment)、SRR（Read Run）
           - SRR是一次测序的数据，与GSM的关系不大。一个GSM可能对应多个SRR（一个样本测多次），一个SRR也可能对应多个GSM的数据（一次测多个样本）。
           - SRP代表一个实验项目，包含多个SRR。
    2. 测序数据下载
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
3. 原始数据质控和比对
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
                    - -o：指定输出目录;-j可指定线程数，还是内核？？
                    - --fastqc:可以质控完后运行FastQC，使用--fastqc_args指定传递给FastQC的参数。
                    - --gzip：将输出结果压缩为gzip格式。
                    - --max_n：一条read在被完全删除之前可能包含的N的总数，别人的示例为4。
                    - -e设置最大错误率：默认为0.1，应该是比较常用的。   
    4. 比对（此处主要指有参比对，而且是基于基因组比对，无参比对可以用Trinity，基于转录本比对可以用RSEM）
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
        - <a href="https://imgse.com/i/pEnG9KS"><img src="https://s21.ax1x.com/2025/02/10/pEnG9KS.png" alt="pEnG9KS.png" border="0"></a>
        - 使用Hisat2软件进行比对（效率高内存少，但输出结果仅为比对文件）
4. 表达定量
    - 相关知识
        - FPKM、RPKM、TPM
    - 教程
        - [转录组分析（四）tophat+cufflinks篇](https://zhuanlan.zhihu.com/p/561285944)
    - 处理原始比对文件的步骤
        - sam格式转bam格式
        - 对bam文件进行排序
        - 去除比对得分较低序列
        - 去除重复reads
    - 使用cufflinks软件进行表达量计算（老，不常用了，效率不高）
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
                - cuffquant是cuffquant能够对单个 BAM 文件的基因转录本表达水平进行定量分析。生成的是CXB文件abundances.cxb，可以作为cuffdiff的输入，这会加快cuffdiff的运行速度。也可以作为Cuffnorm的输入。
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
                - -p：线程数；
                - -b：同cuffquant的-b参数；
                - {transcripts.gtf}：之前的cuffmerge的结果merge.gtf，当然也支持单个样本的gtf文件，或者其他来源的；
                - -L|--lables default: q1,q2,...qN；给每个sample一个样品名或者一个环境条件一个lable； 
                - -T|--time-series：让Cuffdiff来按样品顺序来比对样品，而不是对所有的samples都进行两两比对。即第二个SAM和第一个SAM比；第三个SAM和第二个SAM比；第四个SAM和第三个SAM比...
                - `{sample1_replicate1.sam[,...,sample1_replicateM.sam]} {sample2_replicate1.sam[,...,sample2_replicateM.sam]}... [sampleN.sam_replicate1.sam[,...,sample2_replicateM.sam]]`：所有样本及其重复的bam或sam文件（或者也可以是cuffquant的结果文件CXB files，但请所有样本保持一致），比如有三个样本C1、C2、C3，每个样本2个重复R1、R2，则可以写成`C1_R1.sam,C1_R2.bam,C2_R1.bam C2_R2.bam,C3_R1.bam,C3_R2.bam`，样本重复数可以不一样；
            - cuffnorm主要命令：`cuffnorm --library-type fr-unstranded --output-format cuffdiff -o ./Cuffnorm -q -p 6 -L`
   - <a href="https://imgse.com/i/pEn8zgf"><img src="https://s21.ax1x.com/2025/02/10/pEn8zgf.png" alt="pEn8zgf.png" border="0"></a>
   1. 基因表达定量
   2. 表达矩阵的标准化/normalization
      - <a href="https://imgse.com/i/pEnGPbQ"><img src="https://s21.ax1x.com/2025/02/10/pEnGPbQ.png" alt="pEnGPbQ.png" border="0"></a> 
      - <a href="https://imgse.com/i/pEnDw4O"><img src="https://s21.ax1x.com/2025/02/10/pEnDw4O.png" alt="pEnDw4O.png" border="0"></a>
   3. 质控
      - 样本过滤和基因过滤 
5. PCA和样本相关性分析
   - <a href="https://imgse.com/i/pEnGA5n"><img src="https://s21.ax1x.com/2025/02/10/pEnGA5n.png" alt="pEnGA5n.png" border="0"></a>
   - <a href="https://imgse.com/i/pEnGFEj"><img src="https://s21.ax1x.com/2025/02/10/pEnGFEj.png" alt="pEnGFEj.png" border="0"></a>
6. 差异表达分析
   1. 准备工作
      - 软件：DESeq2
   2. 筛选差异表达基因
   3. 基因名称注释
   4. 差异表达作图
   - <a href="https://imgse.com/i/pEnGkUs"><img src="https://s21.ax1x.com/2025/02/10/pEnGkUs.png" alt="pEnGkUs.png" border="0"></a>
   - <a href="https://imgse.com/i/pEnBQld"><img src="https://s21.ax1x.com/2025/02/10/pEnBQld.png" alt="pEnBQld.png" border="0" /></a>
   - <a href="https://imgse.com/i/pEnGCDg"><img src="https://s21.ax1x.com/2025/02/10/pEnGCDg.png" alt="pEnGCDg.png" border="0"></a>
7. 富集分析
   1. KEGG富集
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