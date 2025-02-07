# <font face="仿宋" color=orange>杂交稻数据调查</font>
##  <center><font face="楷体" size=5>by 藏之地</font></center>
### 一、hybrid_1495_Hanbin_2015
#### 所收集到的数据
- [文献来源](https://www.nature.com/articles/ncomms7258)
- [变异数据](http://db.ncgr.ac.cn/RiceHap4/)
- 表型数据:某一年在杭州的叶宽表型
#### 目的与所遇困难
- 目的：调查特定位点或特定区间的snps的状态
- 所遇困难：该文献的reference和rice3k不一样，不是使用build7版的日本晴
#### 调查三个叶宽基因分hap所定位到的snps在其中的状态
1. **确定变异数据的'H'表示杂合**
这个Mixed文件的格式看上去应该是hapmap[^1]格式的变异形式，暂时就不转换了;
2. **下载对应的注释文件**
> Genomic analysis of hybrid rice varieties reveals 这篇文章用的reference是2005年发表的The map-based sequence of the rice genome.的版本。文章里说的是IRGSP 4.0，大概率就是build 4.0了吧；
> rice3k是用的2013年发表的Improvement of the Oryza sativa里面的版本。
3. **从build4版本的gff（all.con文件）中获取到三个基因的区间**
   1. 确定build7和build4的基因组位置和基因区间大小均不一样
    <a href="https://imgse.com/i/pEi33tJ"><img src="https://s21.ax1x.com/2025/01/14/pEi33tJ.png" alt="pEi33tJ.png" border="0" /></a>
    <a href="https://imgse.com/i/pEi38h9"><img src="https://s21.ax1x.com/2025/01/14/pEi38h9.png" alt="pEi38h9.png" border="0" /></a>
   2. 提取三个基因在build4的基因组的fasta序列
      - 三个叶宽基因信息如下：
    gene	29995741	29999342（MKB3、LOC_Os03g52320、负链）；
    gene	30984785	30995628（NAL1、LOC_Os04g52479、正链）；
    gene	10949596	10958074（NRL2、LOC_Os03g19520、负链） ；
      - 提取fasta序列：
      > 使用samtools提取时报错，无论怎么更换抬头内容都报错，可能是因为fasta的抬头内容，所以先将all.con处理一下再提取：
      <a href="https://imgse.com/i/pEirVyD"><img src="https://s21.ax1x.com/2025/01/14/pEirVyD.png" alt="pEirVyD.png" border="0" width="60%" height="60%"/></a>
      <a href="https://imgse.com/i/pEirEQO"><img src="https://s21.ax1x.com/2025/01/14/pEirEQO.png" alt="pEirEQO.png" border="0" width="60%" height="60%"/></a>
      >> 使用sed命令将之前的all.con的注释行只保留成类似'chr01'的样式；然后和之前一样将三个基因区间写成文件，传入给samtools即可：
      <a href="https://imgse.com/i/pEirZOe"><img src="https://s21.ax1x.com/2025/01/14/pEirZOe.png" alt="pEirZOe.png" border="0" width="50%" height="50%"/></a>
      ```bash
      sed 's/^>\(.....\).*/>\1/' all.con > processed_all.con # 对于'>'开头的行，只保留前面五个字符（反正最后结果是>chr01这种）
      samtools faidx processed_all.con $(cat MKB3_NAL1_NRL2_region) > MKB3_NAL1_NRL2_region_nucleotide
      ```
   3. 确定提取到的是build4三基因所在位置
      - 进行blast:用提取到的build4的基因序列为库，以build7的三基因的CDS序列为query_seq，结果只有NAL1的5条CDS被blast完全匹配
         <a href="https://imgse.com/i/pEFhztO"><img src="https://s21.ax1x.com/2025/01/16/pEFhztO.png" alt="pEFhztO.png" border="0" width="50%" height="50%"></a> 
         <a href="https://imgse.com/i/pEF499e"><img src="https://s21.ax1x.com/2025/01/16/pEF499e.png" alt="pEF499e.png" border="0" width="50%" height="50%"></a>
         <a href="https://imgse.com/i/pEF4ShD"><img src="https://s21.ax1x.com/2025/01/16/pEF4ShD.png" alt="pEF4ShD.png" border="0" width="50%" height="50%"/></a>
      - 手动查看其余的CDS序列
         这里的CDS都是直接samtools提取的，所以这里正负链其实没有啥区别，包括后面确定position也是一样的。 
         结果是其余的未被blast出的CDS序列实际上也是被提取出来的，并且全部都是完全匹配:
         <a href="https://imgse.com/i/pEF43Bq"><img src="https://s21.ax1x.com/2025/01/16/pEF43Bq.png" alt="pEF43Bq.png" border="0" width="80%" height="80%"/></a>
      - 如果降低blast的pvalue（目前为10-5），会发现剩余的也能完全匹配上：
         <a href="https://imgse.com/i/pEFjRGn"><img src="https://s21.ax1x.com/2025/01/17/pEFjRGn.png" alt="pEFjRGn.png" border="0" width="80%" height="80%"/></a>
   4. 将三个基因的两版本对应的pos位点进行对应（之前分hap所用的位点）
   （可以考虑利用文献中的已知位点验证一下，是不是有NAL1？）
      - 根据三基因的blast结果获取到在build4的三基因CDS序列对应的位置
            blast输出格式参考：[blast output format6](https://www.jianshu.com/p/8e838f4aebac)
            <a href="https://imgse.com/i/pEFjW2q"><img src="https://s21.ax1x.com/2025/01/17/pEFjW2q.png" alt="pEFjW2q.png" border="0" width="80%" height="80%"/></a>
            <a href="https://imgse.com/i/pEFj6aQ"><img src="https://s21.ax1x.com/2025/01/17/pEFj6aQ.png" alt="pEFj6aQ.png" border="0" width="60%" height="50%"/></a>
      - 根据位置从build4基因全序列中提取对应的CDS的序列
            <a href="https://imgse.com/i/pEFj2Ps"><img src="https://s21.ax1x.com/2025/01/17/pEFj2Ps.png" alt="pEFj2Ps.png" border="0" width="60%" height="80%"/></a>
      - 将两个版本的CDS的pos进行对应
            <a href="https://imgse.com/i/pEFjc5j"><img src="https://s21.ax1x.com/2025/01/17/pEFjc5j.png" alt="pEFjc5j.png" border="0" width="40%" height="50%"/></a> 
   5. 调查在1495杂交稻变异数据中的对应snps的变异情况
      - 整理出三个叶宽基因的用于分hap的位点
        MKB3如果用老师给的位点来分是可以分出理想的多态的，但是直接用错义突变碱基来分是不行的。NRL2两者都可以分出，但是老师那边的很多位点都不在CDS区间上；所以你想接着做，就得提取基因的全长序列。将基因范围内的全部位点进行对应。
        <a href="https://imgse.com/i/pEkUsI0"><img src="https://s21.ax1x.com/2025/01/19/pEkUsI0.png" alt="pEkUsI0.png" border="0" width="40%" height="50%"/></a>
      - 得到它们对应的build4的位点（可以看到NRL2的某些位点不在CDS上，导致转换失败）
      <a href="https://imgse.com/i/pEkU0qs"><img src="https://s21.ax1x.com/2025/01/19/pEkU0qs.png" alt="pEkU0qs.png" border="0" width="40%" height="50%"/></a>
      - 下载1495杂交稻所有变异数据（貌似这个网站又打不开了）
      暂时下载不了，后面估计也做不下去了。 
4. **整体范围的杂合度**
[^1]:在OneNote的重测序群体遗传学的常用文件下有写