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