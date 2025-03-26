# <font face="仿宋" color=orange>微生物组</font>
#  <center><font face="楷体" size=5>by 藏之地</font></center>
## 一、微生物组入门知识
链接：https://pan.baidu.com/s/1tDF8_rFxFII-Mbwis3EZqA 提取码：0315 
备用链接：http://210.75.224.110/github/Note/Meetting/210623HeFei/210623Hefei.pdf
- [了解宏基因组](https://biobooks.readthedocs.io/zh-cn/latest/12-meta/meta1.html)
1. 微生物组、宏基因组和微生物群的区别
    - 教程
        - [Nature综述：Microbiota, metagenome, microbiome傻傻分不清](https://blog.csdn.net/woodcorpse/article/details/106552424)
        - [What is the meaning of metagenome in genetics?](https://www.cosmosid.com/blog/metagenome-vs-genome-the-differences/#:~:text=The%20metagenome%20refers%20to%20the,considered%20part%20of%20the%20microbiome.)
    - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250318213143.png" width="300" height="300" style="display: block; margin: 0 auto;" /> 
    - 微生物群/Microbiota：指动植物体上共生或病理的微生物生态群体，微生物群包括细菌、古菌、原生动物、真菌和病毒。
    - 微生物组/Microbiome：既包括微生物，又包括其基因组，以及微生物群的产物与宿主环境。
    - 宏基因组/Metagenomics：微生物群的基因和基因组，包括质粒、强调群体的遗传学潜能。
    - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250318215253.png" style="display: block; margin: 0 auto;"/>
    - 各方对宏基因组和微生物组的定义好像有点不一致，可能是侧重点不一样吧，没有谁包括谁。
2. 为什么要研究？
    - 动植物生活环境充满微生物，动植物自身也是微生物共存体；
3. 人类微生物组计划(HMP)
    - HMP计划分为2个阶段：第一阶段，主要以健康人群为对象描绘了人体微生物的全景图；第二阶段，作为第一阶段的延续，联合多组学研究策略探究微生物在健康与疾病中扮演了什么角色。
## 二、实验技术
1. 微生物组中的测序技术：
    - 扩增子测序：
        - 教程：
            - [微生物组学研究-16s测序 从建库到数据分析全流程解析](https://www.bilibili.com/video/BV1Mi4y1M7vY/?spm_id_from=333.337.search-card.all.click&vd_source=2523c7055f0985a7f47ca59739b6b086)
        - 简介
            - 微生物扩增子测序，即16S rRNA/18S rRNA/ITS测序的统称，是以细菌或者真菌为研究对象，通过直接扩增环境微生物总DNA的特定区域，研究微生物分布、丰度变化以及样本间微生物群落组成差异情况。
        - 16S rDNA测序：
            - 16S rDNA是编码16S rRNA的基因，存在于所有细菌和古菌的基因组，长度约1542bp，一般由10个保守区和9个可变区组成。相比于大约120bp的5S rRNA或者2900bp但突变频率较高的23S rRNA，16S rDNA长度适中，既有保守区又有可变区，变异频率低，占细菌总RNA量的80%以上。既能体现不同菌属之间的差异，又能利用测序技术较容易地得到其序列，目前被广泛用于病原菌的检测和鉴定。对16S rRNA的保守区进行设计引物，对可变区的指定区进行扩增测序，用于不同微生物群落的属或种系统进化分类。
                - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250319105759.png"/>
            - 流程
                1. 根据待研究的样本类型确定要测序的目标区域；
                2. 收集样本、提取DNA；
                3. PCR扩增、文库构建（将接头序列引入到序列两端）、测序；
                4. 数据分析：质控、OTU聚类、分类学注释、物种分类和多样性分析（α、β多样性、显著差异菌群分析、菌群标志物预测能力评估、菌群基因功能预测）；
        - 18S rDNA测序：18S rDNA为编码真核生物核糖体小亚基rRNA的DNA序列。在结构上分为保守区和高变区，保守区反映生物物种间的亲缘关系，高变区反映物种间的差异，18S rDNA在进化速率上相对于ITS更加保守。
        - ITS 测序：在真核生物中，18S rDNA和28S rDNA转录间隔序列称为ITS区。ITS由于是非转录区，承受的选择压力较小，变异强。ITS属于中度保守区域，利用它可研究种及种以下的分类阶元。
        - 特点：
            - 需要PCR扩增、便宜、不用考虑宿主污染；
            - 可以鉴定物种多样性到属的水平，相比宏基因组，可以鉴定到低丰度的种类；
            - 只能做到对基因功能的预测，而且一次研究只包括细菌（16S）或真菌（18S或ITS），无法得到全部物种；
            - 建议在初步探究菌群与环境的关系，或大样本量可以使用16S测序；
    - 宏基因组测序
        - 宏基因组测序，以环境中的全部细菌、真菌、古菌等的基因组遗传物质DNA为研究对象，以基因为研究单元，进行物种多样性、基因结构、差异基因和功能基因等的研究。相比于16S扩增子测序，宏基因组测序能够使物种鉴定深度达到“种"，而前者往往只能达到“属”的级别。
        - 特点
            - 无需pcr（二代测序不都是包含pcr这一步的吗？）、可以鉴定物种多样性到种的水平，可以做到功能注释；
            - 可以得到细菌，真菌以及病毒信息，但无法得到基因表达信息；
            - 适合于已初步探明菌群与环境的关系，需要更准确的菌群信息/中等/小规模群体研究；
        - 为什么宏基因组测序比较难？
            - 样品采集和提取：很多样品不好采集（火山、海底淤泥等），同样也就很难有统一测样品提取流程，往往无法提取到高质量的DNA而影响后续分析结果；同时样品中的不同微生物有不同的处理方法，另外一些样品中包含宿主来源的DNA也难以去除，对于宏转录组来说，由于原核生物与真核生物 RNA结构不同，也不能采用同样的测序。
            - 建库测序方面：二代测序需要使用PCR扩增，会带来PCR的偏向性，比如高GC区域无法很好的扩增出来，测序不到，影响后续分析。宏基因组样品由于包含多种GC含量微生物，不同的建库方案会带来差异。相对于单纯的基因组测序来说，数据量巨大；由于来自同一群落，物种的基因组序列常常具有较高相似性，基因组拼接也更为困难；
            - 计算资源：相比于常规基因组或转录组，宏基因组需要更大的计算资源，例如多核心CPU（32线程以上），较大的内存（256G内存以上），相应的分析时间也较长
    - 宏转录组测序
        - 宏转录组测序，以环境中全部RNA作为研究对象，以转录本为研究单元，从整体水平上研究某一特定环境，特定时期，环境微生物群落的全部基因组转录情况以及转录调控规律。
        - 特点
            - 可以得到细菌，真菌以及病毒信息基因表达差异信息；RNA不稳定，建库难度较大，基因信息不完整。
            - 适合已初步探明菌群与环境的关系，重点关注微生物中基因以及代谢通路的丰度变化关系；   
    - 测序技术选择
        - 通常的研究是先做一个16S测序，对物种组成和丰度有一个初步的估计，例如样品中包含了多少种细菌；然后根据评估结果选部分代表样品进行宏基因组测序获取更详细的物种组成和丰度，以及功能组成信息等。然后可以进一步做宏转录组，在转录组水平鉴定宏基因组基因表达情况。简单总结起来三句话：1、what is in my pot? 2、what can they do? 3、what are they doing?
    - 补充：
        - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250319153510.png"/>
        - 推荐：数据分析与图形艺术这本书
        - 宏基因组：死活都可以测到，而宏转录组是测活的；宏病毒组：DNA和RNA都测；
        - 该领域软件（引用过万）：Mothur进行扩增子分析、QIIME（也是扩增子分析）、Usearch等（或者免费版Vseach）；微生物领域常见R包：vegan（多样性分析、环境关联）、phyloseq（进化树）、microbiome（多组学关联）；
        - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250319154809.png"/>
        - ImageGP在线分析网站
2. 文章图表
    - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250318093330.png"/>
    - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250318095820.png"/>
    - 物种组成：一般是在门水平上进行展示；
    - E Venn韦恩图绘制网站；
3. 扩增子分析（类似人口普查）
    - 步骤：DNA提取——扩增子测序——质控、定量、聚类、去噪——多样性分析
    - [微生物组实验手册第二版](https://mp.weixin.qq.com/s/QDrdcwPvTflyYvaSS4BChw)
    - 易扩增子/EasyAmplicon：刘老师的一个扩增子Pipeline；
4. 宏基因组分析（从这个入门比较合适，和转录组更接近）
    - 相比扩增子更全面
    - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250318101341.png"/>
    - 步骤：DNA提取——随机打断、测序——质控、去宿主、组装、注释、比对——物种组成和功能分析
    - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250318101615.png"/>
    - <img src="https://zangvvv-img.oss-cn-nanjing.aliyuncs.com/figure_bed/20250318101742.png"/>
    - 物种丰度评估的两类方法：（两者不可转换） 
        - 基于marker基因：软件MetaPhIAn2；
        - 基于reads数：软件Kraken2；用的更多。
[你想要的宏基因组-微生物组知识全在这(2025.3)](https://mp.weixin.qq.com/s/OG3d-dbtJzJs6eGARXCBdg)
[一文读懂宏基因组分析套路](https://mp.weixin.qq.com/s/T-856Q-BQxdLFDgJoZiOUQ)