## 一、重装系统
### 使用PE进行重装系统
1. 参考教程
	- [视频教程使用PE进行重装](https://www.bilibili.com/video/BV1DJ411D79y?spm_id_from=333.788.videopod.episodes&vd_source=2523c7055f0985a7f47ca59739b6b086&p=2)
	实际上工具都是用的学校的，可以参考[本网站估计需要校园网-南农信息化建设中心 ](http://kms.njau.edu.cn/docs/WinIns.html)
2. 为什么用PE？
	- u盘性能较差，不能运行完整的操作系统。将操作系统简化后才能装在u盘中，这种简化的操作系统就被称之为便携式操作系统，简称PE。可以运行软件，而且可以在u盘上存储多个操作系统。需要准备8G以上的正规u盘。
3. 详细步骤
	- 将PE安装到u盘
      - 下载微PE安装包
      -  插入U盘
      -  打开微PE，将PE安装到U盘
		- 点击全新制作，然后等待3分钟即可完成，其中的EFI分区就是PE所在分区，别动它（可能会被隐藏）；另外一个就是空白分区可以随便放东西
	- 下载win10和激活工具后放到u盘中
	- 然后将u盘插入要重装的电脑
	- 荣耀的BIOS太难用了，不想搞了，恢复出产设置也还是win11，就先用着吧。
## 二、WSL2安装与使用
相比WSL1，WSL2具有真正的linux内核，可以运行docker
1. 准备工作
	- 开启CPU虚拟化
		打开任务管理器——性能——CPU，查看是否开启虚拟化（本机已启用）；如果还未开启，需要进入BIOS，修改一下BIOS设置（intel就是VMXvirtualization，AMD就是一个AMD-v的开关）
	- 开启windows的两个功能
		任务栏搜索“功能”——启用或关闭windows功能——拖到最下面，勾选上“适用于Linux的windows子系统”和“虚拟机平台”，然后按照提示重启电脑；
2. 安装WSL
	- 进入cmd，以管理员身份运行
	- 如果是ubuntu版本参考如下：
    	- 输入`wsl --install --web-download`，回车后开始安装，默认是ubuntu；然后输入应户名和密码；输入`wsl --list --online`可以查看直接在线安装的Linux版本，然后用`wsl --install xxx --web-download`进行安装。
    	- 之后可以linux和windows命令混用。
  	- 输入`wsl -l`查看本机已安装的linux发行版，应该是啥也没有，然后按照提示更新到最新的WSL版本；
3. 下载centos7
	- 参考资料
       - [参考视频](https://www.bilibili.com/video/BV1RL411x7Z1/?spm_id_from=333.337.search-card.all.click&vd_source=2523c7055f0985a7f47ca59739b6b086)
       - [超详细的WSL教程：Windows上的Linux子系统](https://www.bilibili.com/video/BV1tW42197za/?spm_id_from=333.999.0.0&vd_source=2523c7055f0985a7f47ca59739b6b086)
       - [参考博客](https://www.cnblogs.com/zy115/p/18638254)
       - [参考推文1](https://mp.weixin.qq.com/s/PlcxEL5KXDX9e-lpWGzHcQ)
       - [参考推文2](https://mp.weixin.qq.com/s/sM3T9xt5MZG2ZzNvOl9_RA)
	- 进入[WSL版的cantos github地址](https://github.com/mishamosher/CentOS-WSL)，下载centos7.zip文件后解压到D盘；
	- 管理员身份运行centos7.exe，出现complete后退出，再次双击可看到主机名表示安装成功。
    	- <a href="https://imgse.com/i/pEnDaE6"><img src="https://s21.ax1x.com/2025/02/10/pEnDaE6.png" alt="pEnDaE6.png" border="0"></a>
	- 到Microsoft store下载一个终端工具windows Terminal，可以使用centos、cmd和powershell，预览版还支持可视化定制软件界面。
    	- 进入设置——配置文件——centos——外观，修改终端壁纸（默认全黑）
    	- <a href="https://imgse.com/i/pEnDdUK"><img src="https://s21.ax1x.com/2025/02/10/pEnDdUK.png" alt="pEnDdUK.png" border="0"></a>
    	- 如果如果没看到centos可以进设置去添加 
	- 启用和这个终端工具就可以进入cmd、powershell或centos（或者使用centos也可以进入centos）
	

