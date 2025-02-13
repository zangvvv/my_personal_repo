# <font face="仿宋" color=orange>转录组练习</font>
# <center><font face="楷体" size=5>by 藏之地</font></center>
## 一、文献中RNA-seq流程
1. 序列质量评估：FastQC
2. 过滤接头和低质量序列：Trimmomatic (0.36)
3. mapping:TopHat2 (2.1.0)
4. 基因表达定量：Cufflinks (2.2.1)
    - Expressed genes were defined as those with FPKM > 1.
6. 筛选差异基因：R包DESeq2
    - adjusted p value < 0.05 and log2 fold-change > 1.5.
## 二、原始数据下载
1. 实际操作
    1. 用prefetch下载序列
    2. 提取成fastq
## 三、质控
参考张一柯设置的参数，单个序列的代码为：`trimmomatic PE -phred33 SRR10751892_1.fastq SRR10751892_2.fastq ./tmp_trim/SRR_92_r1.clean.fq.gz ./tmp_trim/SRR_92_r1.unpaired.fq.gz ./tmp_trim/SRR_92_r2.clean.fq.gz ./tmp_trim/SRR_92_r2.unpaired.fq.gz LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 -threads 12 MINLEN:36`
