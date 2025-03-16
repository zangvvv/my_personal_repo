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
    - 教程
        - [Zotero6无痛升级Zotero7！（超简单）](https://blog.csdn.net/weixin_46091817/article/details/143857282/)
        - [Zotero 7 插件-合集教程](https://www.bilibili.com/video/BV12H4y1w75j?spm_id_from=333.788.videopod.sections&vd_source=2523c7055f0985a7f47ca59739b6b086)
    - 可以从原来的zotero6的检查更新里面直接更新到zotero7，但是这个是32版本的，随后我还是从官网下载了64位版本的zotero7；这样是覆盖安装的，同步的账户和文献都有；zotero connector更新一下也是可用的。zotero7新增功能：鼠标放在引用上面直接显示引文信息或图片预览。
    - 先尝试将拯救者升级到7，然后在honor上安装7吧
    - 插件安装：
        - zotero6升级到7之后，会有很多插件diabled；可以下载“zotero插件市场”这个插件，首先下载[该插件下载地址](https://github.com/syt2/zotero-addons/releases/latest/download/zotero-addons.xpi)，然后点击“工具——Plugins——设置小图标——install plugin from file”，选择xpi文件安装，安装之后就可以在主界面搜索栏左侧看到一个插件小图标；右击插件即可更新或安装卸载，如果找不到某个插件可以右下角更新插件源后刷新看看。
        - 有一些插件暂时还不适配zotero7：zotfile，但是可用[zotero attenger代替，但是据说bug不少](https://blog.csdn.net/weixin_44628096/article/details/144422089)
        - 新安装的插件：
            - zotero pdf rename：
            - Sci-PDF：可能是更方便从scihub下载文献的插件，没用过；
            - awesome gpt(之前的gpt不知道是不是同一个)：[大致流程：注册——APIkeys——填写设置](https://www.bilibili.com/video/BV1q1wRemE6n?spm_id_from=333.788.videopod.sections&vd_source=2523c7055f0985a7f47ca59739b6b086)
            - [Zotero ID](https://github.com/qnscholar/zotero-if)：比较新的影响因子；
        - 之前就有的插件：茉莉花、GPT、Better Notes for Zotero、Translate for Zotero、ZotCard、Zotero Style（更新成Ethereal Style）、Zotfile、Zotero PDF Preview(Zotero7自带对pdf的预览，只不过只能左右，所以不用下载，并且也没有)
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
