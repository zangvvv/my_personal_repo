# <font face="仿宋" color=orange>微生物组</font>
#  <center><font face="楷体" size=5>by 藏之地</font></center>
## 一、文献中的流程和代码
[复现文章，未看](https://mp.weixin.qq.com/s/xTEWKHdIe676TePvMGHTew)
## 二、准备工作
1. 环境配置和软件下载
建立名为metagenome的环境，下载包：bamm、bwa、diamond、eggnog-mapper、khmer、kraken、prodigal、quast、report（R包）、seqtk、spades、vsearch、seqkit（有一个抽样的功能）、pigz（重新压缩，最后并未使用）；
其它需要用的包：sratools软件已经放到全局的环境变量、samtools可以在leaf_width_vvv环境里；
实际使用到的软件：prefetch（sratools内含）、skewer
2. 下载sra数据
2. 转成fastq
写在`get_fastq_gz.sh`脚本里
3. 抽样
由于数据已经比较小了，所以抽样并不必要，但是只对一个样本使用seqkit进行抽样，结果一致；
## 三、质控
1. 使用skewer去除barcode等非生物序列
    - 教程：
        - [Skewer User’s Manual](https://sourceforge.net/projects/skewer/)
        - [基本上照搬软件自带的帮助文档](https://www.jianshu.com/p/daa580f484c7)
    - 简介与安装
        - 是一款良好的barcode去除软件，用于双端测序序列barcode的去除；
            - 关于adapter和barcode（或者说index barcode）的区别：
                - index/barcode：用于区分同一测序池中的不同样本的标签。它是adapter的一部分。[Illumina adapters include "barcodes' in the adapter itself](https://www.biostars.org/p/468398/)
        - 直接conda安装成功。
    - 简单用法：
        - 主要命令：`skewer -x AGATGTGTATAAGAGACAG -m head -1 -t 6 --quiet ./unpack/SUBERR793599_1.fq.gz 2>> skewer.head.log|skewer -x CTGTCTCTTATACACATCT -m tail -t 6 --quiet -1 - 2>> skewer.tail.log > ./skewer/SUBERR793599_1.fq`表示先修剪5'端的接头（head），再修剪3‘端的接头序列。
            - -x: 第一条reads的接头序列或fasta文件，默认为'AGATCGGAAGAGCACACGTCTGAACTCCAGTCAC'；
            - -y：第二条reads的接头序列或fasta文件，默认为'AGATCGGAAGAGCGTCGTGTAGGGAAAGAGTGTA'；
            - -m|--mode {string}：对双端reads，
                - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250321181555.png"/>
            - -1|--stdout：是否打印到标准输出，默认是不打印。
            - -t：线程数；
            - --quiet：不显示进度；
            - -M|--matrix {string}：接受一个制表符分隔的文件，将正向和反向的接头序列写在一个文件中，有了-x和-y就不用了吧；