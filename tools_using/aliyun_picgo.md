# <font face="仿宋" color=orange>利用github建立个人图床</font>
#  <center><font face="楷体" size=5>by 藏之地</font></center>
## 一、前言
1. 什么是图床
    - image hosting是指在线存储和管理图片的服务平台，用户可以将图片上传到图床，然后通过生成的链接（url、html、Markdown等格式）在不同的地方引用、展示这些图片。
2. 为什么选择阿里云OSS建立图床
    - github搭建图床以及路过图床等免费图床的缺点
    github是个大网站，应该不容易挂掉；加上我目前使用的路过图床总是说我用了vpn（就算我关掉加速器还是这样报错），我想要找一个替代品；github作为图床有一些限制，比如好像一个repository只能放一个G还是500M的东西，而且上传到的图片质量和大小都会受到限制。而且很慢，经常图片不显示；而且500M太少了，一张图几百k，一千张都不要，而且听说picgo项目也没用了
    - 路过图床有时访问不上而且听说会删图片；
    - 阿里云等提供的对象存储服务（简称OSS，就是一种云存储），可以作为图床使用，支持高效的图片上传、存储和访问。
3. 教程来源
    - [Typora+PicGo+阿里云OSS服务](https://developer.aliyun.com/article/1033730)
    - [b站视频](https://www.bilibili.com/video/BV12t4y147Gs/?spm_id_from=333.337.search-card.all.click&vd_source=2523c7055f0985a7f47ca59739b6b086)
## 二、步骤
1. 购买阿里云服务
    - 登录后，选择对象存储OSS，进入购买；资源包类型就是标准存储包，规格的话个人使用40G足够；
2. 创建bucket
    - 进入管理控制台——创建bucket：自定义{bucket_name}，地域选择最近的，读写权限设置为公共读；
        - bucket_name设置为`zangvvv-img`；
        - 地域为南京
        - 读写为私有， 
3. 创建个人key
    - 点击进入bucket列表，点击自己的bucket名称，新建自己要用来做图床的目录——点击概览，复制节点域名{oss开头}——鼠标放到右上角个人账户，点击"AccessKey管理"——先点击继续使用AccessKey，然后再点击创建AccessKey——创建完成后，复制AccessKey ID和AccessKey Secret；
        - 新建目录为`figure_bed` 
        - 节点域名为`oss-cn-nanjing.aliyuncs.com`
    - 现在不建议开通个人key 
4. 下载picgo和图床配置
    - 可以去github下载对应的版本；
    - 点击图床设置——点击阿里云OSS，填写设置：
        - 设定KeyId：之前的AccessKey ID
        - 设定KeySecret：之前的AccessKey Secret复制过来
        - 设定存储空间名就是之前的{bucket_name}
        - 确认存储区域就是之前的{ossxx}
        - 存储路径为之前新建目录名、然后点击确定，设置为默认图床
但是说阿里云有被流量盗刷的风险，但是我的md不准备发布（至少读研期间不会发布），所以应该就没事吧（虽然不发布，只要上云就是可爬取的）
## 附加内容--Cloudflare
免费空间和流量，但是首先需要有信用卡或者是一个国区的paypal账号用于添加付费计划，同时你还需要一个域名，所以暂时就不搞了[用Cloudflare R2 + PicGo，搭建最佳个人图床！免费、稳定、国内/国外都能访问](https://www.bilibili.com/video/BV1eZCrY4E17/?spm_id_from=333.337.search-card.all.click&vd_source=2523c7055f0985a7f47ca59739b6b086)