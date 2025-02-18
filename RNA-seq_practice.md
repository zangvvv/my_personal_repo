# <font face="仿宋" color=orange>转录组练习</font>
# <center><font face="楷体" size=5>by 藏之地</font></center>
## 一、文献中RNA-seq流程
1. 序列质量评估：FastQC
2. 过滤接头和低质量序列：Trimmomatic (0.36)
3. mapping:TopHat2 (2.1.0)
4. 基因表达定量：Cufflinks (2.2.1)
    - 参数设置：Expressed genes were defined as those with FPKM > 1.
6. 筛选差异基因：R包DESeq2
    - 参数设置：adjusted p value < 0.05 and log2 fold-change > 1.5.
## 二、原始数据下载
1. 实际操作
    1. 用prefetch下载序列
    2. 提取成fastq
## 三、质控
参考张一柯设置的参数，单个序列的代码为：`trimmomatic PE -phred33 SRR10751892_1.fastq SRR10751892_2.fastq ./tmp_trim/SRR_92_r1.clean.fq.gz ./tmp_trim/SRR_92_r1.unpaired.fq.gz ./tmp_trim/SRR_92_r2.clean.fq.gz ./tmp_trim/SRR_92_r2.unpaired.fq.gz LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 -threads 12 MINLEN:36`
## 四、建立基因组索引
已经确认下载的基因组文件和gff文件所用的染色体序号一致；然后将基因组的fa文件和所建立的索引放在一个目录下；
由于需要索引文件前缀和基因组文件前缀一样，所以统一改成(我转录组也改了)，`Oryza_sativa_mh63.MH63RS2.60`，建立基因组索引的代码为：`bowtie2-build -t 12 ./MH63_genome_index/Oryza_sativa_mh63.MH63RS2.60.fa ./MH63_genome_index/Oryza_sativa_mh63.MH63RS2.60`花费了大概7分钟
## 五、比对
建立转录组索引，`tophat2 -G Oryza_sativa_mh63.MH63RS2.60.gff3 --transcriptome-index=MH63_genome_index/Oryza_sativa_mh63.MH63RS2.60.tr ./MH63_genome_index/Oryza_sativa_mh63.MH63RS2.60` 花费了大概3分钟。
接着mapping， `tophat2 -p 8 -o ./4_mapping_tophat --transcriptome-index=MH63_genome_index/Oryza_sativa_mh63.MH63RS2.60.tr MH63_genome_index/Oryza_sativa_mh63.MH63RS2.60 tmp_trim/SRR_92_r1.clean.fq.gz tmp_trim/SRR_92_r2.clean.fq.gz` 成功！耗时52分钟
## 六、表达定量
`cufflinks -o 5_cufflinks/ -p 12 -g Oryza_sativa_mh63.MH63RS2.60.gff3 --library-type fphat/accepted_hits.bam` 耗时35分钟，中间有过暂停很久不动的情况。
## 张一柯
fastqc、trimmomatic质控，star比对，用subread的featureCounts;


