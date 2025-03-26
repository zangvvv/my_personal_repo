# <font face="仿宋" color=orange>生物数据库使用</font>
#  <center><font face="楷体" size=5>by 藏之地</font></center>
介绍生物数据库的简单使用
## 大型数据库
1. NCBI
    - 简介
    - 利用NCBI进行参考基因组下载：（手写笔记上有其它方法）
        - [保姆级参考基因组及其注释下载教程（图文详解）](https://cloud.tencent.com/developer/article/1842208) [从NCBI中下载各物种参考基因组](https://www.cnblogs.com/rmliu/p/15656379.html)
            - 打开NCBI官网——输入物种的拉丁名（以便唯一确定物种信息）——进入Taxonomy目录下的该物种界面——点击Reference Genome下的download——一般选择RefSeq来源的参考基因组，以及相关注释文件等进行下载；（但是有些水稻版本是没有RefSeq来源的相关文件的哦，包括gff文件）
        - [wget指令下载UCSC的基因组文件（NCBI类似）](https://www.cnblogs.com/sqsgoodluck/p/15902138.html) 
            - [NCBI ftp站点](https://ftp.ncbi.nlm.nih.gov/genomes/all/GCF/001/433/935/GCF_001433935.1_IRGSP-1.0/)
2. ENA
3. ensemble
    - [ensembl的ftp站点](https://ftp.ensemblgenomes.ebi.ac.uk/pub/plants/release-57/)
## 物种特有数据库
1. 水稻
    - [水稻常见基因组数据库-2022-02-17](https://www.jianshu.com/p/50906de2302d)
    - [rap-db](https://rapdb.dna.affrc.go.jp/)
        - 可以进行基因检索、基因ID转换等。
    - [MSU-RGAP](http://rice.uga.edu/index.shtml)
        - 通过gene ID可以和RAP-db进行基因名的转换，还可以进行go和kegg的搜索。近几年的水稻基因组高分文章基本上都是采用的它的命名规则。
    - [籼稻注释网站RIGW](http://rice.hzau.edu.cn/rice_rs3/)
        - 华中农的籼稻品种珍山97（ZS97）和明恢63（MH63），有ID转换工具。
    - [Gramene](https://www.gramene.org/)
        - 整合了许多不同水稻亚种的基因组数据。
    - [3K Rice Genome Project](http://snp-seek.irri.org) 
## 临时内容    
1. 关于正负链和正义反义链（手写笔记上有）
    - 教程： 
        - [编码基因是在正链还是负链上？](https://www.zhihu.com/search?type=content&q=%E5%9F%BA%E5%9B%A0%E5%9C%A8%E6%AD%A3%E9%93%BE%E8%BF%98%E6%98%AF%E8%B4%9F%E9%93%BE%EF%BC%9F)
        - [What is the strand specificity of a reference genome?（看第二个回答）](https://biology.stackexchange.com/questions/36595/what-is-the-strand-specificity-of-a-reference-genome)
        - [正反链（forward and reverse strand）和正义反义链（sense and antisense strand)以及链特异性建库的理解](https://www.cnblogs.com/wangprince2017/p/9809181.html)
        - [DNA正负链（包含例子）](https://blog.csdn.net/qq_37774098/article/details/125730737)
        - [](https://www.biostars.org/p/3423/)
        - [DNA链方向上的小疑惑](https://www.jianshu.com/p/2a118933d716)
    - 参考基因组存储的是正链，反链就是其互补配对生成的那条；而正义反义链是指的基因的编码链（正义、非模板链）和互补链（反义、模板链，在转录过程中与RNA结合）；也就是说，有一些基因的正义链在参考基因组上，还有一些基因的正义链在参考基因组的互补链上；
2. 测序过程的正负链 
    - https://www.zhihu.com/question/388371598
    - https://www.zhihu.com/question/315117413
    - https://www.jianshu.com/p/b18ee79a0285
    - https://www.jianshu.com/p/56aa023decd1 
