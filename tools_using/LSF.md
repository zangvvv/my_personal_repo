# <font face="仿宋" color=orange>南农超算和LSF作业管理系统使用</font>
#  <center><font face="楷体" size=5>by 藏之地</font></center>
## 一、前言
1. 背景
    - LSF（load sharing facility）是IBM旗下的一款分布资源管理的工具，用来调度、监视、分析联网计算机的负载，几乎支持所有的主流操作系统，作用上与PBS一样，使用方法略有不同；
2. 教程（更多的参考qq群里的资料）
    - [LSF基础和使用（很详细）-2022-04](https://mp.weixin.qq.com/s/u3OQqA7sXyj1iOrrWgUJ1g)
    - [5-Job管理](https://www.bilibili.com/video/BV1dekwY2EiM?spm_id_from=333.788.videopod.sections&vd_source=2523c7055f0985a7f47ca59739b6b086)
3. 一些概念
    - login节点：用于用户登入、提交作业、安装编译软件、上传或下载数据的主机；
    - compute节点：运行任务作业的主机，南农的普通计算节点CPU核数为28，共38个节点，单个节点内存256G；默认申请配额为56核，1T存储；
    - queue队列：定义了哪些用户可以使用哪些资源
    - 作业核数：用户运行作业需要调用的compute主机的核心数量
    - 串行作业
    - 并行作业
4. 超算平台使用注意事项
    - 禁止在login直接运行计算任务。Login节点功能为登录，上传下载数据，提交作业，安全软件。
    - 任务测试可直接ssh compute01节点后直接运行任务，最大测试时间为60分钟，测试没问题后任务需通过Isf提交作业任务。
    - 集群公共软件路径：`/gss1/env/`和`/gss1/env_os7/`下；集群公共数据库路径：`/gss2/database/`下。
    - 未通过作业调度系统提交运行的、占用的CPU资源显著高于在作业调度系统中请求的资源，会被kill掉；
    - 大内存任务提交到big01和big02节点上；
## 二、LSF作业提交
1. bsub命令直接提交
    - 与PBS不同，大部分情况下，可以不需要写作业脚本，直接一行命令就可以提交作业；使用展示如下：
        - <a href="https://imgse.com/i/pEQocd0"><img src="https://s21.ax1x.com/2025/02/21/pEQocd0.png" alt="pEQocd0.png" border="0"></a>
    - 主要命令：`busb -n Z -q QUEUENAME -o OUTPUTFILE COMMAND`
        - -n Z：提交作业需要的线程数；
        - -q QUEUENAME：指定作业提交的队列。如果不添-q选项，系统将把作业提交到默认的作业队列；
        - -J：
        - -i inputfile：表示程序要读入的文件名（不常用）；
        - -o OUTPUTFILE表示输出文件名，作业提交后的输出到标准输出信息将会保存在这个文件中，一般情况下，outputfile作为日志文件；
        - COMMAND：就是你的程序命令，可以不用""括起来，也可是自己的脚本（需要用python xxx.py，如果是sh脚本前面也要加一个sh）；
            - 对于串行作业，对于串行作业，COMMAND可以直接使用您的程序名。例如，将串行程序mytest的通过LSF提交到normal队列：`bsub-n 1-q normal -o mytest.out ./mytest`
            - 对于MPI并行作业，COMMAND的格式为mpich_gm PROG_NAME。例如，将并行程序mytest，通过LSF提交，使用16个线程运行这个作业：`bsub-n 16 -q normal-o mytest.out mpich_gm mytest`
    - bsub命令使用注意事项
        - 一般情况下，如果你用bsub提交任务只需在你提交的命令前加上bsub，然后回车提交即可，然后使用bjobs查看提交作业的状态；当你提交任务后，立即输入bjobs，此时的状态一般是PEDN（STAT那一列），这是任务在等待分配，并不是错误信息。数秒过后，将会显示RUN状态。
        - 如果先书写bsub，则后面不支持tab补齐功能。建议大家，先书写提交的任务，最后在前面添加bsub；
        - bsub不支持如shell中的文本处理工具，如awk，sed、、grep、1等等命令行，若想提交此类任务，务必编写lsf脚本提交此类作业；如下图所示，我想用shell中的管道命令使用bsub提交任务。
2. 使用lsf脚本进行提交
    1. 首先创建一个lsf后缀或者sh后缀的脚本文件yourjob_lsf，内容如下：
        - 串行作业lsf脚本（单节点） 
            ```
            #BSUB -J bowtie：作业名称，也可以不写
            #BSUB -n 10：核心数
            #BSUB -R span[hosts=1]：节点数，生信软件中支持跨节点的比较少，一般就单节点，如不确定程序是否支持多节点并行，请勿使用，避免资源浪费。
            #BSUB -o %J.out：%J表示作业ID，即此处的作业输出文件为 jobid.out，如果不想要输出文件可使用`-o /dev/null`；
            #BSUB -e %J.err
            #BSUB -q normal
            {your command}
            ``` 
        - 并行作业lsf（多节点，还有待补充） 
            ```
            #BSUB -J MPIJob
            #BSUB -q normal
            #BSUB -n 400：此脚本申请了400核；
            #BSUB -R "span[ptile=20]"：20个节点，每个节点20个核；
            #BSUB -W 10:00：作业最长运行时间，超过这个时间会被kill掉，格式为hh:mm；
            #BSUB -o stdout_%J.out 
            #BSUB -e stderr_%J.err：-o和-e表示append, -oo和-eo表示overwrite；
            mpich_gm {your command}：也就是对于MPI并行作业，需要在命令前加一个mpich_gm；
            ```  
    2. 使用`bsub < yourjob_lsf`提交作业
    - <a href="https://imgse.com/i/pEQo6Zq"><img src="https://s21.ax1x.com/2025/02/21/pEQo6Zq.png" alt="pEQo6Zq.png" border="0"></a>
3. 交互式作业（忽略）
    - 目前只允许在interactive队列使用交互模式，不建议使用，有测试需求的通过ssh compute01节点直接运行命令测试。命令为：`bsub-q interactive-Is bash`
4. 其它常用命令
    - bjobs：查看当前作业的状态（例如，是否在运行、是否排队），直接使用`bjobs`
        - -a：显示所有作业当你使用提交过任务后，可以使用`bsub-a`参数去查看所有提交的任务状态，无论是结束的还是正在进行的。如果你的任务状态是EXIT，则代表你的命令有误。
        - -w选项可以显示作业的详细信息，包括作业的状态和信息（宽格式）。
        - -r：显示当前正在运行的作业；
        - -p：显示等待运行（pending）的作业和等待的原因；
        - -s：显示已经挂起（suspending）的作业和挂起的原因；
        - -l：查看作业详细信息；
        - -sum：查看所有未完成作业的汇总信息；
        - `bjobs -1 JOBID`：显示JOBID这个作业的所有信息；
        - 作业的状态有：PREN：正在排队、RUN：正在运行、DONE：正常退出、EXIT：异常退出、SSUSP：被系统挂起、USUSP：被用户自己挂起；
    - bhist：查看历史作业的详细信息，包括运行时间、资源使用情况等，`bhist -l`
    - bpeek：查看作业输出，适用于调试作业时查看标准输出和错误输出，`bpeek {job_id}`
    - bstop：`bstop jobid` 挂起作业，如被系统挂起（节点超负荷系统会自动挂起该节点上的部分作业），则作业状态为SSUSP；如被用户自己或管理员挂起，则作业状态为USUSP；使用`bresume bjoid` 继续运行被挂起的作业；
    - bkill：用于停止一个正在运行的作业或取消一个排队中的作业，`bkill {job_id}`
    - bqueues：查看集群中所有队列的信息，包括队列状态和资源使用情况，直接使用`bqueues {队列名}`
    - lsload：查看整体负载，`lsload {compute_num}`：查看某个节点的负载
    - bhosts：查看所有节点核心数使用情况，各列分别为：
        -  
## 批量进行作业处理
1. 方法一：建立一个shell脚本，脚本内包括shell的for循环将命令写成一个个脚本，然后用for循环去使用bsub命令提交作业；
2. 直接在shell脚本中执行for循环并使用bsub提交命令；
3. 写成一个循环的lsf脚本
三种都是有效的；
（第一个批量20分钟，第二个50分钟，第三个花费4个半小时，前两个都是多个bsub作业，最后一个只有一个作业）