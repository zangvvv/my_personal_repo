# <font face="仿宋" color=orange>荣耀magicbook新机操作</font>
#  <center><font face="楷体" size=5>by 藏之地</font></center>
## 一、win11相关
1. 系统预装就是win11，工程师建议还是用11，而且荣耀的BIOS很难用。
2. [关闭广告](https://www.bilibili.com/video/BV1NsiyYwEwu/?spm_id_from=333.337.search-card.all.click&vd_source=2523c7055f0985a7f47ca59739b6b086)
3. [win11设置右键默认显示更多](https://www.cnblogs.com/yjung/p/18205317)
4. 触控板失效
    - [笔记本触摸板按键不能用、不能双指三指滑动和双指单击右键解决办法]()右键windows图标，
## 二、软件
1. 大内存的如qq和微信就装在D盘，小内存（wps、网盘、）放在C盘。
2. VScode
    - 同步（很早就做过）
        - 右下角设置中登入github或Microsoft账户，并设置同步选项（全选了）[不用settings sync插件了](https://mp.weixin.qq.com/s/6wNNElCLbUL0mRdxz3HuLw)；下载后先登入就可以直接同步所有扩展以及设置。
    - [设置tab为四个空格](https://mp.weixin.qq.com/s/_wP3C5_mb8mJsnt4aEqcpQ)
        - 注意，如果写Markdown直接回车还是2个空格，可能是Markdown插件没有修改；
    - VScode下载比较简单就是比较难删除，还是直接安装在c盘；
3. OneNote
    - win11有自带的吗？好像由于我在win11登入的是学生版的office，所以对应的OneNote也是2016的。反正都可以同步在云端的。
4. xshell
    - 设置选中即复制，右键即粘贴：点击工具——点击选项——点击终端——向右按钮设置为"粘贴剪贴板内容"，并勾选"将选定的文本自动复制到剪贴板"
5. zotero
    - 从zotero6更新到zotero7
        - [好像可以直接](https://blog.csdn.net/weixin_46091817/article/details/143857282/)
    - 先尝试将拯救者升级到7，然后在此电脑上安装7吧
6. anaconda
    1. 下载anaconda安装程序
        - 打开[Anaconda 官方网站](https://www.anaconda.com/download)。登陆后点击Free Download免费下载适合自己操作系统的anaconda安装包；
        - 或者使用一些大学的开源软件镜像站[清华大学镜像站](https://repo.anaconda.com/archive/)
    2. 运行安装程序并接受许可协议
    3. 选择安装类型并设置安装路径（建议放在非系统盘，保证路径中不要出现中文或特殊符号，例如空格或感叹号等）
    4. 高级安装选项
        - Register Anaconda as the system Python：表示将使anaconda成为系统中的默认Python版本，这意味着当您在任何命令提示符窗口中输入 python 时，系统将默认使用 Anaconda 的 Python 解释器。如果 Anaconda 是您唯一的或主要的 Python 环境，推荐勾选此项。这样可以让其他程序，如 VSCode 或 PyCharm，自动识别 Anaconda 为系统的主 Python 版本。
        - Clear the package cache upon completion：安装完后清楚缓存包；
    5. 配置环境变量
        - 开始菜单——输入环境变量——编辑系统环境变量——高级——环境变量——
