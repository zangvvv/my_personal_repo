# <font face="仿宋" color=orange>tophat2_cufflinks_pipeline</font>
#  <center><font face="楷体" size=5>by 藏之地</font></center>
## 第一步 使用tophat2软件进行比对
- 教程(目前不太用，速度慢，到2016年已停止更新)
    - [Tophat2比对原理及命令-2018-07](https://mp.weixin.qq.com/s/v553kZ8SrpOxhvm6kIultQ)
    - [使用tophat2和cufflinks组合进行有参转录组分析(较详细,还涉及安装的报错处理)-2023-02](https://mp.weixin.qq.com/s/EtYSc_q1gyDDu9DZxR7VGA)
    - [Tophat2 & Bowtie2 Tutorial (MAC)(视频教程）-2015-08](https://www.youtube.com/watch?v=7_yY-PuVN3U)
    - [生信软件|bowtie2（测序序列与参考序列比对）-2023-06](https://blog.csdn.net/u011262253/article/details/79833969)
    - [转录组学助力：索引文件构建(tophat2、star、hisat2简单使用)-2023-11](https://mp.weixin.qq.com/s/9RdWTbu3XS5q1lwk_UitNg)
    - [转录组学初体验 ｜ 2.4 Tophat与Tophat2（参数--threads是不是写错的，还有tophat2比对的命令也有错误，不用.fa）-2024-06](https://mp.weixin.qq.com/s/VbXYw1a4MEzWXMYkcQSRcA) 
1. 第一步：在基因组网站上下载基因组文件和对应的注释文件
    - yotube视频直接在ensembl上下载的是genome.fa而不是toplevel，而且这个细菌基因组很小，建立索引时直接一个文件名和前缀就完了；
    - 水稻MH63应该下载的是dna_index下面的toplevel.fa.gz那个吧。ensembl上的注释文件倒是只有一个。具体要参考文献吧。
2. 第二步：使用bowtie2软件来对基因组文件建立索引（5-10min）
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
3. 第三步：使用tophat2进行比对
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
            - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/41e11343bff515c5eb8cda2ccb97d5c.png"/> 
            - accepted_hits.bam：以BAM文件的形式储存着比对信息，这些比对信息是依据染色体的顺序排列储存的；可用samtools查看，如果第一行中出现`SO:coordinate`——指bam文件**已经按照基因组坐标排序过了**；是后面cufflinks的输入文件。
            - junctions.bed：是以Bed格式储存着发现的剪接位点，每个位点都含有左右两个部分，每个部分长度是Read能够跨越剪接位点匹配到基因组的最远距离，最终的得分是跨越剪接位点的Read数量。
            - insertions.bed：包含着查询到的插入位点信息，chromLeft指的是插入到基因组的位置。
            - deletions.bed：包含着查询到的缺失位点信息，chromLeft指的是缺失片段的第一个碱基。
            - unmapped.bam：没有map上的序列
            - align_summary.txt能够显示比对效率以及多少Read能够多处比对；`concordant pair alignment rate`显示比对率，一般75-90%，如果是某几条染色体则会相应降低。
## 第二步 使用cufflinks软件进行表达量计算
- 教程：（老，不常用了，效率不高）
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
   - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/a1c334cdc6539ffa5ccf75ee0e4b837.png"/>
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
## 第三步 筛选差异基因
1. 使用CummeRbund R包进行cuffdiff软件后续的分析（不推荐）
   - 教程（还有官方的那篇paper）：
       - [cummeRbund:用于Cufflinks高通量测序数据的可视化软件包（基本没用）-2017-09](https://mp.weixin.qq.com/s/m4sJE5B_O00ltab89ZV2Uw)
       - [Pipeline RNA-seq-github(2017)](https://github.com/johanzi/RNA-seq_pipeline?tab=readme-ov-file#cufflinks-pipeline)
       - [bioconductor](https://www.bioconductor.org/packages/release/bioc/html/cummeRbund.html)
   - 安装与简介
       - 是cuffdiff做RNA-seq的下游工具；CummeRbund创建了一个SQLite数据库，将cuffdiff运行产生的结果都存储到数据库中，将genes、transcripts、transcriptionstart sites、以及CDS建立关联。将这些数据存储到数据库中，并建立相关的索引，就可以很容易的对多个样本之间或者其他条件的数据进行查询检索，允许用户对于单个或者一组基因的各种feature进行比较分析。同时还提供了诸多的绘图函数，可以满足一般的数据可视化需要。
       - 使用`BiocManager::install("cummeRbund")`进行安装；
   - 使用方法：由于最终没有完成，故没有参考代码。
2. 根据  