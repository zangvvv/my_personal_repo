# <font face="仿宋" color=orange>生物数据库使用</font>
#  <center><font face="楷体" size=5>by 藏之地</font></center>
主要介绍一些通用的生信软件和工具。
1. 基因ID转换
    - 教程：
        - [关于基因ID的二三事（Entrez Gene、RefSeq、Ensembl、UniProt、芯片ID）](https://zhuanlan.zhihu.com/p/148446919)
        - [常见生物信息基因ID（Entrez Gene、RefSeq、Ensembl、UCSC、UniProt）及ID转换工具](https://cloud.tencent.com/developer/article/1638291)
        - [RefSeq前缀](https://www.plob.org/article/3711.html)
        - [使用biomaRt做水稻ID转换及相关信息的填充](https://www.jianshu.com/p/480c46ec1629)
    - 认识相关基因ID
        - Entrez ID：来自于ncibi的gene数据库，与其对应的还有以NM和NP开头的转录本ID和蛋白ID（称为RefSeq ID）；
        - Ensembl ID：以ENSG、ENST、ENSP开头的ID，分别对应gene、转录本和蛋白；
        - UniProt ID：蛋白注释的ID；
2. 学习使用gffread将gff转换成gtf：
    - 教程：
        - [2019-08-21 gffread----gff、gtf格式转换](https://www.jianshu.com/p/c7e8d0a6fdff)
        - [生信软件｜01 gffread软件入门使用教程](https://mp.weixin.qq.com/s/4W3TM1rgz1UcAfsliRNSIg) 
    - 安装与简介
        - 直接conda安装成功；
        - 可以根据注释文件提取基因组文件的cds序列、蛋白质序列、外显子文件等；同时也可以完成gtf和gff文件的相互转换
    - 使用方法：gff文件转成gtf文件：`gffread xxxx.gff -T -o xxxx.gtf`以及将gtf文件转成gff文件 `gffread xxxx.gtf -o xxxx.gff`
        - -T：输出文件会是gtf而不是gff3。 