# <font face="仿宋" color=orange>Git与github使用</font>
#  <center><font face="楷体" size=5>by 藏之地</font></center>
## 一、Git和Github简介
- Git是一个运行在电脑上的版本控制软件，Github就是基于Git这个版本控制软件打造的网站；
- Git的三个概念：commit（提交，就是提交项目开发过程的历史记录）、repository（仓库，差不多是代码所在的文件，对应Github的项目首页）、branch（分支，可以根据不同的人和物在当前代码中开辟不同的分支）；Github就是将Git共享到网络上；
## 二、github网站的使用
1. [下载别人项目的代码、上传自己的项目代码](https://www.bilibili.com/video/BV1hS4y1S7wL/?spm_id_from=333.337.search-card.all.click)
   - 在github将别人项目的源码打包下载
   点击源码右上角的Code，选择Download ZIP即可，这个项目下的所有文件和文件夹都会被下载下来。（而且不需要登录就可下载）
   - 在github上创建自己的新项目
   点击右上角头像左边的加号，选择create a new repository，然后起名、描述、选择add a README file后github会给你生成一个README.md文件，其余默认即可完成创建。
      - 上传文件：在自己的项目右上角选择Add file，点击Upload files，然后从本地拖动到github后点击commit change即可。（直接拖动没有Git的版本控制等作用）
      - github支持在线修改代码（小铅笔图标）
      - 删除仓库：settings-general拉到最底部就行；删除文件：点击文件-右上角三点标志-选择delete file-点击commit changes才算完全删除。
2. 其它
   - 在源码区域的下面会自动显示源码中的README.md文件的内容；
   - 作者的一个Repository就是一个项目；issue可以完成与作者或其他友友互动，如果是自己的项目可以关闭话题；
   - watch、star和fork的区别
     - watch就是表示你关注这个项目，可以选择作者更新或项目发生任何动静就给你发邮件提醒；
     - star就是收藏这个项目；
     - fork就是参与到这个项目中，相当于你将当前状态下的原项目copy了一份，然后对其进行优化和改动；
## 三、[版本控制软件Git的使用](https://www.bilibili.com/video/BV1Cr4y1J7iQ?spm_id_from=333.788.videopod.sections&vd_source=2523c7055f0985a7f47ca59739b6b086)
1. Git软件下载和安装
   - Git - Downloading Package ([git-scm.com](https://git-scm.com/downloads/win))
   - 目前选择的是Setup，一路下一步即可，放在D:\LenovoSoftstore\Git目录下；
   - 安装好之后右击桌面菜单（在任意目录中右击都有）里会多了两个Git的选项；选择Git Bash进入Git控制台；
2. 使用Git
   - 修改Git界面样式:
     进入Git控制台后单击上面的四色Git图标，选择options，选择Text——select，修改样式后保持；
   - 要配置Git用户名和邮箱（我就用github注册用的qq邮箱和zangvvv）
      - `git config --global user.name “xx”（用户名）`
      - `git config --global user.email “xx@xx.com”`，这里的邮箱可以随便写；
      - `git config --global --list` 查看当前账户下这两个参数所对应的具体内容；
   - Git中的代码管理常用命令
      - git clone命令用于从支持Git的代码托管网站(如github)上下载源码
         - 具体过程如下：选择某一个本地文件夹下右击进入Git bash控制台，然后从github的Code获取到该项目的https链接（或ssh）；使用`git clone https://xxxxx`将项目克隆到当前文件夹；
         - 其中的.git文件夹包含版本管理的相关文件。
      - git init命令可以将自己本地的项目用Git进行版本管理
         - 做法如下：在你项目所在的本地文件夹下右击进入Git bash控制台，输入git init进行初始化，此时会产生一个.git的文件夹（可能被隐藏了而不显示），用于管理即将新建的源代码；
      - git commit命令将项目下指定文件或所有文件的代码复制到Git仓库进行备份
         - 使用`git add .`命令（或`git add --all`）将当前文件夹下所有文件放入暂存区；再使用`git commit -m "xxx"`将源代码保存到仓库中；可以使用`git log`查看提交的历史记录
            - 其中"xxx"表示对本次提交的备注，比如：功能一已完成；
         - 如果项目某一个功能模块只完成了一半，想要将其它已完成的模块文件和文件夹上传到仓库；你可以使用`git add 文件名1 文件名2 …` 将指定文件和文件夹上传到仓库；然后使用`git commit -m "xxx"`添加本次描述；
            - `git config/*` # 某个文件夹下所有文件
            - `git home/*.php` # 指定后缀的所有文件
         - `git checkout`命令将某个文件恢复到最近一次提交的版本
            使用`git checkout HEAD xx文件` 命令会将目录下的xx文件覆盖成上一次提交时的版本。
## 四、[二者实际应用——手把手教你用git上传项目到GitHub](https://zhuanlan.zhihu.com/p/193140870)
1. [将git的ssh key配置到github](https://blog.csdn.net/lqlqlq007/article/details/78983879)
   - 在Git生成ssh key（这个ssh key也可以用于gitee等其他支持Git的平台）
      - 在Git bash控制台上输入：`ssh-keygen -t rsa -C "2693828240@qq.com"`（不要出现中文引号，ssh后没有空格）；然后三次回车就可以完成ssh key的生成；可以`cd ~/.ssh`目录后用less命令查看id_rsa.pub文件的内容或者进入C盘的目录下找到这个文件；复制这个文件的全部内容；
         - id_rsa是私钥（私钥只保存在本地，也可以设置私钥的密码，但我没有）、id_rsa.pub是公钥（用于与github等平台建立连接）；如果换一台电脑，可以将私钥和公钥复制到新电脑的同一目录，但建议重新生成）
         - <a href="https://imgse.com/i/pEmApM6"><img src="https://s21.ax1x.com/2025/02/07/pEmApM6.png" alt="pEmApM6.png" border="0" width="80%" height="80%"/></a>     
   - 给github配置ssh key（配置公钥）
      - 进入github账户的设置，找到SSH and GPG key，选择add new SSH key，填好标题和上面复制的key，点击完成即可；（一般选择Authentication key）
      - <a href="https://imgse.com/i/pEmkzxx"><img src="https://s21.ax1x.com/2025/02/07/pEmkzxx.png" alt="pEmkzxx.png" border="0" width="80%" height="80%"/></a>         
   - 将Git与github连接（因为github已经配置了公钥）
      - ssh -T git@github.com
      - <a href="https://imgse.com/i/pEmkOIJ"><img src="https://s21.ax1x.com/2025/02/07/pEmkOIJ.png" alt="pEmkOIJ.png" border="0" width="80%" height="80%"/></a>
2. 将本地项目上传到github
   - 首先在github上新建一个远程仓库，复制到其仓库链接（比如我的临时仓库 https://github.com/zangvvv/test_repo.git）
   - 构建本地项目的本地Git仓库/将本地项目用Git管理：
      - 在本地项目的目录右击进入Git bash控制台（也可以cd进入），输入下面的代码，将本地项目建立成Git仓库：
         - `git init` # 初始化一次就行，后面不用再init啦
         - `git add .`，建议直接使用`git init -b main`
         - `git commit -m "本次提交的描述"`
         - <a href="https://imgse.com/i/pEmkji9"><img src="https://s21.ax1x.com/2025/02/07/pEmkji9.png" alt="pEmkji9.png" border="0" width="70%" height="80%"/></a>
   - 将本地Git仓库与github远程仓库关联：
      - 输入`git remote add origin https://github.com/zangvvv/my_personal_repo.git`
   - 上传本地项目到github：
      - 输入`git push -u origin main -f`
         1) <a href="https://imgse.com/i/pEmkvGR"><img src="https://s21.ax1x.com/2025/02/07/pEmkvGR.png" alt="pEmkvGR.png" border="0" width="80%" height="80%"/></a>
         2) <a href="https://imgse.com/i/pEmkxR1"><img src="https://s21.ax1x.com/2025/02/07/pEmkxR1.png" alt="pEmkxR1.png" border="0" width="80%" height="80%"/></a>
      - 其它相关
         - 报错：error: src refspec main does not match any
           - 本地仓库用的是master分支，而远程的github默认是main分支；可以使用`git branch`查看，然后使用`git checkout -b main`将本地仓库的branch设置为main分支；然后重新尝试推送即可。
         - 关于main和master branch：
            - branch是Git中指向提交记录的可移动指针，用于在同一个仓库中管理多个开发路线；在团队协作中，每个开发者通常会在自己的分支上工作，完成后通过 Pull Request 提交更改。
            - Git 2.28版本（2020年）以后，默认项目的开发的主干分支由master改为main，可以使用git branch -M main将之前的分支名改为main；可以git branch查看分支名称；
            - 教程使用的是master，全部替换成我上面的git push -u origin main -f
         - -u参数（可略过）
            - -u是--set-upstream 的缩写，用于设置当前本地分支（例如 main）与远程分支（如 origin/main）之间的关联（即上游分支）。git push -u origin main表示设置 （远程）main 分支的上游分支为 origin/main。
         - git push和git pull命令
            - git pull 用于从远程仓库获取最新的代码并与本地代码合并（因为可能在github直接编辑）
               git pull <远程仓库名> <分支名>
               git pull origin main # 示例
            - git push 用于将本地仓库中的提交上传到远程仓库。
               git push <远程仓库名> <分支名>
               git push origin main# 示例
            - 如果之前运行过 git push -u origin main 或者是 git push -u origin <分支名>，你已经设置了默认推送分支，之后只需运行 git push 即可将当前本地的文件传到对应分支；
   - 如果本地项目有修改，只需先ssh到github，然后git add和git commit将文件或项目放进暂存区，然后git push到github就行；（第一次上传就是多了将本地项目存放到Git仓库、与远程github关联、设置默认分支这几个步骤）
      - `ssh -T git@github.com`
      - `git add .`
      - `git commit -m "final_test"`
      - `git push`
      - <a href="https://imgse.com/i/pEmA9sK"><img src="https://s21.ax1x.com/2025/02/07/pEmA9sK.png" alt="pEmA9sK.png" border="0" width="60%" height="70%"/></a>
 
