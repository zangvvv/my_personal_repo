# <font face="仿宋" color=orange>转录组练习</font>
# <center><font face="楷体" size=5>by 藏之地</font></center>
## 一、文献中RNA-seq流程
1. 序列质量评估：FastQC
2. 过滤接头和低质量序列：Trimmomatic (0.36)
3. mapping:TopHat2 (2.1.0)
4. 基因表达定量：Cufflinks (2.2.1)
    - 参数设置：Expressed genes were defined as those with FPKM > 1.
6. 筛选差异基因：R包DESeq2
    - 参数设置：adjusted p value < 0.05 and log2 fold-change > 1.5.；
7. 练习数据有513个Sample（但是文中说的是510个数据），也就是对应了513个GSM编号；共有ChIP-seq（355 samples）、RNA-seq（58）、FAIRE-seq（58）、BiSulfite-seq（38）、ChIP-reChIP-seq（4）5个数据集，即对应5个GSE编号，共513个样本。
## 二、准备工作
1. 构建项目目录以及参考序列下载
    - 原始数据目录raw_data
    - 基因组文件（ensembl、JGI等或物种专用网站）和注释信息文件ref
    - 质控之后的结果clean_data
    - 结果文件：比对结果和定量结果
2. 原始数据下载或上传
    1. 用prefetch下载序列以及提取成fastq文件
        - 进入GEO后选择RNA-seq数据，点击进入RNA-seq的GSE数据，点击`SRA run selector`进入样本下载界面；
        - 选择好样本后导出样本，使用`prefetch -f no -p --option-file SRR_mh63_nippobare_rna-seq.txt`进行下载得到sra文件；
    2. 提取成fastq：
        - 写在get_fastq_gz.sh里面；并且解压了一条gz文件，发现大小和之前解压成fastq的文件大小一致，说明应该没错；多久给忘了，大概几个小时吧；
## 三、数据预处理
1. 质量控制（指导数据问题在哪）
    - fastqc和MultiQC
    - 使用`ls fastq_gz/*_1.fastq.gz | sed 's/_1.fastq.gz//' | xargs -I {} echo "nohup fastqc -t 12 -o ../1_fastqc {}_1.fastq.gz {}_2.fastq.gz &" > process_fastqc.sh` 先生成脚本，然后执行；由于并行执行，耗时5分钟。并且查看了结果与之前的单个样本去运行一样，说明执行无误；
    - 使用multiqc：`python -m multiqc ./fastqc`，其中-m表示用 Python 运行一个模块，而不是一个脚本文件；一分钟不到吧；
2. 质量过滤
    - 去接头：multiqc结果中有2个是不过关的，分别是SRR10751908_1和SRR10751908_2，可以过滤的接头是Illumina Universal Adapter；（trimmomatic也可以过滤自己指定的特定接头序列）
        - <a href="https://imgse.com/i/pEQcCX8"><img src="https://s21.ax1x.com/2025/02/20/pEQcCX8.png" alt="pEQcCX8.png" border="0"></a> 
    - 使用find ~/anaconda3/ -name *TruSeq3-PE*可以查看到trimmomatic自带的Illumina接头序列
        - <a href="https://imgse.com/i/pEQcKXT"><img src="https://s21.ax1x.com/2025/02/20/pEQcKXT.png" alt="pEQcKXT.png" border="0"></a> 
        - <a href="https://imgse.com/i/pEQcucV"><img src="https://s21.ax1x.com/2025/02/20/pEQcucV.png" alt="pEQcucV.png" border="0"></a>
        - <a href="https://imgse.com/i/pEQcQnU"><img src="https://s21.ax1x.com/2025/02/20/pEQcQnU.png" alt="pEQcQnU.png" border="0"></a>  
参考张一柯设置的参数，单个序列的代码为：`trimmomatic PE -phred33 SRR10751892_1.fastq SRR10751892_2.fastq ./tmp_trim/SRR_92_r1.clean.fq.gz ./tmp_trim/SRR_92_r1.unpaired.fq.gz ./tmp_trim/SRR_92_r2.clean.fq.gz ./tmp_trim/SRR_92_r2.unpaired.fq.gz LEADING:3 TRAILING:3 SLIDINGWINDOW:4:15 -threads 12 MINLEN:36`
## 四、建立基因组索引
已经确认下载的基因组文件和gff文件所用的染色体序号一致；然后将基因组的fa文件和所建立的索引放在一个目录下；
由于需要索引文件前缀和基因组文件前缀一样，所以统一改成(我转录组也改了)，`Oryza_sativa_mh63.MH63RS2.60`，建立基因组索引的代码为：`bowtie2-build -t 12 ./MH63_genome_index/Oryza_sativa_mh63.MH63RS2.60.fa ./MH63_genome_index/Oryza_sativa_mh63.MH63RS2.60`花费了大概7分钟
1. 正式版本`bowtie2-build -t 12 ./3_index/Oryza_sativa_mh63.MH63RS2.60.fa ./3_index/Oryza_sativa_mh63.MH63RS2.60`，结果和之前一致；
## 五、比对
建立转录组索引，`tophat2 -G Oryza_sativa_mh63.MH63RS2.60.gff3 --transcriptome-index=MH63_genome_index/Oryza_sativa_mh63.MH63RS2.60.tr ./MH63_genome_index/Oryza_sativa_mh63.MH63RS2.60` 花费了大概3分钟。
1. 正式版本：`tophat2 -G Oryza_sativa_mh63.MH63RS2.60.gff3 --transcriptome-index=3_index/Oryza_sativa_mh63.MH63RS2.60.tr ./3_index/Oryza_sativa_mh63.MH63RS2.60`，结果也和之前一样；
2. mapping的正式版本
   ```
    #!/bin/bash
    # 遍历生成脚本
    for file in `ls /gss1/home/huangju02/hj/vvv/RNA-seq_practice/2_clean_data/*_paired.fastq.gz`
    do
    j=`basename $file` # 这一步是提取出文件名，而"${j%%_*}"会提取到样本名
    echo "tophat2 -p 14 -o /gss1/home/huangju02/hj/vvv/RNA-seq_practice/4_mapping_tophat/result_${j%%_*} --transcriptome-index=/gss1/home/huangju02/hj/vvv/RNA-seq_practice/3_index/Oryza_sativa_mh63.MH63RS2.60.tr /gss1/home/huangju02/hj/vvv/RNA-seq_practice/3_index/Oryza_sativa_mh63.MH63RS2.60 \
    /gss1/home/huangju02/hj/vvv/RNA-seq_practice/2_clean_data/${j%%_*}_1_paired.fastq.gz /gss1/home/huangju02/hj/vvv/RNA-seq_practice/2_clean_data/${j%%_*}_2_paired.fastq.gz" > \
    /gss1/home/huangju02/hj/vvv/RNA-seq_practice/0_data/lsf_path/${j%%_*}_mapping.sh
    done

    # 提交队列
    for i in `ls /gss1/home/huangju02/hj/vvv/RNA-seq_practice/0_data/lsf_path/*_mapping.sh`;do
    bsub -n 24 -q normal -R span[hosts=1] -o %J.out -e %J.err sh ${i}
    done
   ``` 
接着（单样本的）mapping， `tophat2 -p 8 -o ./4_mapping_tophat --transcriptome-index=MH63_genome_index/Oryza_sativa_mh63.MH63RS2.60.tr MH63_genome_index/Oryza_sativa_mh63.MH63RS2.60 tmp_trim/SRR_92_r1.clean.fq.gz tmp_trim/SRR_92_r2.clean.fq.gz` 成功！耗时52分钟
## 六、表达定量
转录本组装：（批量的写在脚本里）
`cufflinks -o 5_cufflinks/ -p 12 -g Oryza_sativa_mh63.MH63RS2.60.gff3 --library-type fr-unstranded 4_mapping_tophat/accepted_hits.bam` 耗时35分钟，中间有过暂停很久不动的情况。
转录本比较：(批量的写在脚本里，但由于运行很快，所以直接在compute01中运行了)
`cuffcompare -r Oryza_sativa_mh63.MH63RS2.60.gff3 -o tmp_cuffcompare/SRR10751892 tmp_cufflinks/transcripts.gtf`
整合转录本：也很快（5分钟不到），直接运行，单样本的做不了就不用做了
`cuffmerge -o 7_cuffmerge/ -g Oryza_sativa_mh63.MH63RS2.60.gff3 -p 16 assembly_list.txt `
结果是一个merged.gtf文件和一个logs目录；
转录本定量：（批量的写在脚本里，耗时大概2个多小时）
`cuffquant -o tmp_cuffquant/ -p 16 -u --library-type fr-unstranded Oryza_sativa_mh63.MH63RS2.60.gff3 tmp_mapping_2/accepted_hits.bam`，单样本的大概十几分钟吧
### 差异表达分析cuffdiff
1. 首先获取SRR编号与样本及重复数对应关系
    - 因为之前所有的结果都是以SRR样本号进行标记的，而进行差异表达分析的时候需要对样本进行分组，所以首先去sra数据下载地址，下载到所选样本的metadata文件（csv格式，本文所有样本重复数为2）
    - 复制run和source_name两列内容保存到一个metadata.txt文件中；
2. 执行cuffdiff
    - 三个样本的话大概半个小时：(批量的写在cuffdiff.lsf脚本里，一共8个样本，每个样本各2个重复，耗时2个多小时)
        ```
            cuffdiff -o tmp_cuffdiff/ -p 16 -L MH63_yf,MH63_pc,MH63_rt -u 7_cuffmerge/merged.gtf 8_cuffquant/quant_SRR10751892/abundances.cxb,8_cuffquant/quant_SRR10751893/abundances.cxb 8_cuffquant/quant_SRR10751894/abundances.cxb,8_cuffquant/quant_SRR10751895/abundances.cxb 8_cuffquant/quant_SRR10751896/abundances.cxb,8_cuffquant/quant_SRR10751897/abundances.cxb
        ``` 
### 标准化cuffnorm
三样本测试大概三分钟就有结果了（全部样本的写在cuffnorm.lsf脚本里，耗时也只有两三分钟）
`cuffnorm -o tmp_cuffnorm/ -p 16 -L MH63_yf,MH63_pc,MH63_rt --no-update-check 7_cuffmerge/merged.gtf 8_cuffquant/quant_SRR10751892/abundances.cxb,8_cuffquant/quant_SRR10751893/abundances.cxb 8_cuffquant/quant_SRR10751894/abundances.cxb,8_cuffquant/quant_SRR10751895/abundances.cxb 8_cuffquant/quant_SRR10751896/abundances.cxb,8_cuffquant/quant_SRR10751897/abundances.cxb`
## 张一柯
fastqc、trimmomatic质控，star比对，用subread的featureCounts;


