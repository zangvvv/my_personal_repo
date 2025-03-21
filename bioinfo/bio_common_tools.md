# <font face="仿宋" color=orange>生物信息学常用软件</font>
#  <center><font face="楷体" size=5>by 藏之地</font></center>
不是特定组学才能用的软件
## 通用软件
1. [多线程压缩工具pigz](https://mp.weixin.qq.com/s/94Ki8qF-KopknkBjbtqcQw)
    - 简介常用于压缩fastq的软件是gzip，但是它不支持并行处理，对于体积较大的测序数据，压缩起来比较费时；并且有时在多线程任务中套着一步gzip的压缩命令，也会导致所占用的CPU有部分空闲。
    - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250321164358.png"/>
    - 建议一般1G大小的数据，2-4个线程既高效又合理利用资源；几十G的文件或者增大压缩比例，则可增加到8-10个线程。对于解压缩，相对于压缩没有那么复杂，两者没有太大区别，大文件或者已知压缩比相对较高的文件可考虑使用pigz。
    - 常用命令：
        - 压缩：`pigz -p {int} -c test.fastq > test_pigz.fastq.gz`；解压缩：`pigz -p {int} -dc test_gzip.fastq.gz > test.fastq`；解压缩后再压缩：`pigz -p {int} -dc ./rawdata_0.01/SUBERR793599_2.fq.gz | pigz -p {int} > ./SUBERR793599_2.fq.gz`
        - -p：线程数；
        - -d：解压；
        - -c：解压输出到标准输出/stdout；
        - -k：保留原始文件；
        - -l：查看压缩率；
        - -0到-9：压缩等级，数字越大压缩率越高，速度越慢，默认为6；
