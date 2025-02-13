<!-- TOC -->

- [<font face="仿宋" color=orange>Markdown入门教程</font>](#font-face%E4%BB%BF%E5%AE%8B-colororangemarkdown%E5%85%A5%E9%97%A8%E6%95%99%E7%A8%8Bfont)
    - [<center><font face="楷体" size=5>来自b站毛怪</font></center>](#centerfont-face%E6%A5%B7%E4%BD%93-size5%E6%9D%A5%E8%87%AAb%E7%AB%99%E6%AF%9B%E6%80%AAfontcenter)
        - [一、准备工作](#%E4%B8%80%E5%87%86%E5%A4%87%E5%B7%A5%E4%BD%9C)
        - [二、基本语法](#%E4%BA%8C%E5%9F%BA%E6%9C%AC%E8%AF%AD%E6%B3%95)
    - [二级标题](#%E4%BA%8C%E7%BA%A7%E6%A0%87%E9%A2%98)
        - [三级标题](#%E4%B8%89%E7%BA%A7%E6%A0%87%E9%A2%98)
        - [三、其他操作](#%E4%B8%89%E5%85%B6%E4%BB%96%E6%93%8D%E4%BD%9C)
        - [四、导出为pdf](#%E5%9B%9B%E5%AF%BC%E5%87%BA%E4%B8%BApdf)

<!-- /TOC -->

# <font face="仿宋" color=orange>Markdown入门教程</font>
##  <center><font face="楷体" size=5>来自b站毛怪</font></center>
### 一、准备工作
1. **安装vscode**
2. **下载必要的插件**
    - Markdown All in One
    - Markdown Preview Enhanced
    - Markdown PDF（up不推荐，比如latex公式渲染失败）
    - Paste Image
    - 补充插件Auto Markdown TOC（可以一键生成目录，不要就直接删除前面那些内容就可以，也可以右键——delete）
3. **开始使用：** 创建.md文档，打开侧边同步预览功能，开始编辑
### 二、基本语法
1. **标题或目录层级**
    ## 二级标题
    ### 三级标题
2. **列表**
    1. 下面这种是无序列表
        - 列表1
        + 列表2
        * 列表3
    2. 这种有数字的就是有序列表，注意列表嵌套和缩进层级密切相关
    3. to do list
        - 可以直接点击来实现完成项的编辑
        - [x] a
        - [ ] b
        - [ ] c
3. **表格**
    - 第一行写表头名，第二行写对齐方式，第三行之后写具体内容
    - 用冒号位置控制对齐方式，'-'的数量不用管，写内容时会自动扩展出足够的空间
        | 左对齐 | 居中对齐 | 右对齐 |
        | :----- | :------: | -----: |
        | a      |    b     |      c |
4. **段落**
    - 如何进行换行：两个以上空格后回车或者中间直接空一行（实际效果因编辑器而异，比如Jupyter中可以用\来换行）
    - 分割线: 三个*号或者三个-号
    ---
    ***
    
    - 字体
      - 其中下划线由于用到了html代码，要用代码块展示
        |     字体      |      代码      |
        | :-----------: | :------------: |
        |    *斜体*     |      * *       |
        |   ==高亮==    |     == ==      |
        |   **粗体**    |     ** **      |
        | ***斜粗体***  |    *** ***     |
        |   ~~删除~~    |     ~~ ~~      |
        | <u>下划线</u> | ```<u> </u>``` |
    - 脚注
        - 首先要在后面对脚注内容进行解释
        请大家多多三连[^1],一个学习更多Markdown的链接[^2]
5. **代码**
    - 代码块
        ```javascript
        function greet(name) {
          console.log("Hello, " + name + "!");
            }
 
        greet('World');
        # 这是一段代码块，```之后可以接语言种类会显示一些高亮效果，但是好像不能显示语言标记
        ```
    - 句内代码
    `一小句代码`，同一行可以接其他文字

6. **超链接**
    - [本文档的视频教程连接](https://www.bilibili.com/video/BV1bK4y1i7BY/?spm_id_from=333.1391.0.0&vd_source=2523c7055f0985a7f47ca59739b6b086)
    - [图床工具工具推荐：路过图床][路过图床]
    - [定制VScode的markdown快捷键]https://mark9804.netlify.app/breves/tools-and-workflows/customize-vscode-markdown-shortcuts
    以上三种方法均可 
7. **插入图片**
    - 需要先将本地图片上传到图床，然后复制图床给的markdown地址或者html地址（后者可以实现更多图片的操作，比如大小位置等）
    - 使用markdown语法插入图片
      [![pEPdTJO.png](https://s21.ax1x.com/2025/01/12/pEPdTJO.png)](https://imgse.com/i/pEPdTJO) 
    - 使用html语法插入图片可以改变位置和大小
      <a href="https://imgse.com/i/pEPdqQH"><div align=center><img src="https://s21.ax1x.com/2025/01/12/pEPdqQH.png" alt="pEPdqQH.png" border="0" width="60%" height="60%"/></div></a>
### 三、其他操作
- **插入引用块**
    > 引用块可以对一大段文字进行一个补充或强调
    > > 可以写一些注意事项或注释
    > >
    > > - 或者引用一些别人的话
- **插入latex公式**
  - 行内显示公式
    $f(x)=ax+b$  
  - 块内显示公式
    $$
    \begin{Bmatrix}
    a & b \\
    c & d
    \end{Bmatrix}
    $$
- **html、css语法**
  - ctrl+shift+p搜索"Markdown Preview Enhanced:Customize CSS"
    在style文件中可以使用css语法改一些markdown的标题格式等，笔记将第一级标题设置为居中：
    <a href="https://imgse.com/i/pEPwG01"><div align=center><img src="https://s21.ax1x.com/2025/01/12/pEPwG01.png" alt="pEPwG01.png" border="0" width="80%" height="80%"/></div></a>
  - 更多个性化设置
    更多markdown的个性化设置可以从File-Preferences-Settings找到更多markdown的设置，比如之前用Jupyter notebook的markdown设置过的每级标题的字号
### 四、导出为pdf
- **直接使用插件效果不好**
- **up建议**：右击——在浏览器打开——另存为pdf
<a href="https://imgse.com/i/pEPwNtK"><div align=center><img src="https://s21.ax1x.com/2025/01/12/pEPwNtK.png" alt="pEPwNtK.png" border="0" width="60%" height="70%"/></div></a>

[路过图床]: https://imgse.com/
[^1]: 所引用的内容
[^2]: https://markdown.com.cn/editor/