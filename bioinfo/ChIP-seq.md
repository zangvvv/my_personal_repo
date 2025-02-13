# <font face="仿宋" color=orange>ChIP-seq原理与分析流程</font>
#  <center><font face="楷体" size=5>by 藏之地</font></center>
## 一、ChIP-seq背景介绍
1. 表观遗传学
   - 表观遗传：DNA序列不发生改变，但基因的表达却发生了可遗传的改变，即基因型未发生变化而表型却发生了变化。主要包括组蛋白修饰、DNA修饰、染色质构象改变。其中组蛋白修饰就是用到了ChIP-seq的技术。
2. ChIP-seq
   - ChIP-seq（染色质免疫共沉淀测序技术）：是将染色质免疫沉淀技术（Chromatin Immunoprecipitation，ChIP）与二代测序技术相结合，是研究体内**蛋白质与DNA相互作用**的有利工具，通常用于**转录因子结合位点**（转录调控）或者**组蛋白特异性修饰位点**（表观调控）的研究。
4. ChIP-seq应用
   <a href="https://imgse.com/i/pEmab3n"><img src="https://s21.ax1x.com/2025/02/08/pEmab3n.png" alt="pEmab3n.png" border="0"></a> 
   - 组蛋白修饰（表观调控）
      - 什么是组蛋白修饰
        - 组蛋白八聚体的基本单元上都有一段富含赖氨酸（K）和精氨酸（R）的小尾巴；它们是裸露在核小体的外面，因此很容易被一些修饰酶催化，产生各种修饰基团。
      - 常见的组蛋白修饰有：
      - ChIP-seq就是通过加上特异的组蛋白抗体，它可以识别组蛋白尾部的某个特定修饰（如H3K4me3）；然后将复合物沉淀下来，对其中的DNA进行测序就可以知道这种修饰发生在染色体的哪些区域。
   - 转录因子与DNA的结合（转录调控）
      - 大致原理类似，不过这里的抗体识别的可能是蛋白的特定结构域或氨基酸序列。
## 二、实验技术
1. 实验材料
2. 实验原理和流程
   - <a href="https://imgse.com/i/pEmaH9s"><img src="https://s21.ax1x.com/2025/02/08/pEmaH9s.png" alt="pEmaH9s.png" border="0"></a>
   - 甲醛交联
      提取组织和细胞后，使用甲醛将DNA和与其结合蛋白质在空间上固定在一起，使他们de相互作用在实验过程中保持稳定，从而防止其解离。交联后的细胞进一步被裂解。
   - 片段化
      使用超声或酶切等方法将裂解后的染色质打断成较小的DNA片段，通常为200-1000bp，方便后续的免疫沉淀。
   - 加入目标蛋白抗体
      选择能特异性识别目标蛋白质（转录因子或修饰过的组蛋白等）的抗体，与抗体结合之后，目标蛋白质和与之结合的DNA片段形成免疫复合物。（这一步就是将目标DNA从整体中pull down下来，或者说是进行IP/免疫沉淀；与IP相对的有一个input样本，它是未经过免疫沉淀处理的样本，代表样本的原始背景）
   - 洗涤
      清洗免疫沉淀物，以去除非特异性结合的DNA和蛋白质，确保仅保留有特异性抗体结合的DNA-蛋白质复合物。
   - DNA纯化
      在利用高温等方法破坏交联之后，通过酚/氯仿抽提或柱式纯化方法纯化出DNA片段，去除其他杂质。
   - 测序
      将纯化后的DNA片段进行高通量测序。  
## 三、ChIP-seq分析流程
1. QC/质控
   - 去接头、去低质量的序列：fastp
      <a href="https://imgse.com/i/pEmy5fU"><img src="https://s21.ax1x.com/2025/02/08/pEmy5fU.png" alt="pEmy5fU.png" border="0" width="60%" height="50%"/></a>
   - 质量评估：fastqc
   - mapping：bwa-mem
      <a href="https://imgse.com/i/pEm6EAP"><img src="https://s21.ax1x.com/2025/02/08/pEm6EAP.png" alt="pEm6EAP.png" border="0" width="60%" height="30%"/></a> 
   - 去掉多重比对的reads以及重复的reads：samtools
   - call_peak:macs2（已经有3了）
      <a href="https://imgse.com/i/pEm0l0f"><img src="https://s21.ax1x.com/2025/02/08/pEm0l0f.png" alt="pEm0l0f.png" border="0" width="40%" height="50%"/></a>
      <a href="https://imgse.com/i/pEm0178"><img src="https://s21.ax1x.com/2025/02/08/pEm0178.png" alt="pEm0178.png" border="0" width="40%" height="50%"/></a>
      <a href="https://imgse.com/i/pEm08AS"><img src="https://s21.ax1x.com/2025/02/08/pEm08AS.png" alt="pEm08AS.png" border="0" width="40%" height="50%"/></a> 
   - motif分析：homer
      <a href="https://imgse.com/i/pEm0QnP"><img src="https://s21.ax1x.com/2025/02/08/pEm0QnP.png" alt="pEm0QnP.png" border="0" width="40%" height="50%"/></a>
2. 分析
  - merge peak：不同样本call出来的peak可能是不一致，这样就不能比较，将peak merge之后就能比较了；
      <a href="https://imgse.com/i/pEm0u6I"><img src="https://s21.ax1x.com/2025/02/08/pEm0u6I.png" alt="pEm0u6I.png" border="0" width="50%" height="50%"/></a>
  - 不同组间的compare
      <a href="https://imgse.com/i/pEm0n1A"><img src="https://s21.ax1x.com/2025/02/08/pEm0n1A.png" alt="pEm0n1A.png" border="0" width="50%" height="50%"/></a>
      <a href="https://imgse.com/i/pEm0KXt"><img src="https://s21.ax1x.com/2025/02/08/pEm0KXt.png" alt="pEm0KXt.png" border="0" width="50%" height="50%"/></a>
  - 富集分析
      <a href="https://imgse.com/i/pEmRNI1"><img src="https://s21.ax1x.com/2025/02/08/pEmRNI1.png" alt="pEmRNI1.png" border="0" width="50%" height="50%"/></a> 
3. 与转录组的关联分析
  - 基因的表达与表观信号的整体关联
     <a href="https://imgse.com/i/pEnicvV"><img src="https://s21.ax1x.com/2025/02/09/pEnicvV.png" alt="pEnicvV.png" border="0" /></a>- 
  - 差异表达基因对应的表观信号分布
      <a href="https://imgse.com/i/pEnihE4"><img src="https://s21.ax1x.com/2025/02/09/pEnihE4.png" alt="pEnihE4.png" border="0"></a>
  - 差异峰区域相关的基因的表达变化情况（与上面相反）
      <a href="https://imgse.com/i/pEniWbF"><img src="https://s21.ax1x.com/2025/02/09/pEniWbF.png" alt="pEniWbF.png" border="0"></a>
  - 差异表达基因与差异peak的关联
      <a href="https://imgse.com/i/pEniRDU"><img src="https://s21.ax1x.com/2025/02/09/pEniRDU.png" alt="pEniRDU.png" border="0"></a>



