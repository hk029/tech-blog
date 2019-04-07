---
title: GitBook入门（用github做出第一本书）
date: 2016-05-23
categories: 
- 工具
tags: 
- gitbook
init: 1
---
我最近接触到gitbook，发现它支持markdown和git，刚好把我之前在github上的笔记可以生成一本书，于是我就开始着手捣鼓gitbook，一下午的时间就弄的差不多了，说明这个东西还是挺容易的，可以看看我的书：[《LeetBook（LeetCode详解）》](https://www.gitbook.com/book/hk029/leetbook/details)。

比较建议直接在github上部署你的框架，然后再导入gitbook。


# 创建一个新的仓库

![Alt text](http://img.hksite.cn/2019-03-29-064955.png)

创建过程不需要我细讲了把，如果不知道，看看[github入门](1)

![Alt text](http://img.hksite.cn/2019-03-29-064959.png)

创建一个新文件，名为SUMMARY.md，里面填入：
```
# Summary
* [前言](README.md)
```
# 创建一本书
首先进入gitbook的官网：https://www.gitbook.com/
![Alt text](http://img.hksite.cn/2019-03-29-065003.png)

点红色部分，用github登录

登录之后，点+NewBook
![Alt text](http://img.hksite.cn/2019-03-29-065007.png)

点到github，点击Link to your Github
![Alt text](http://img.hksite.cn/2019-03-29-065013.png)

授权完毕然后再点到这个页面
![Alt text](http://img.hksite.cn/2019-03-29-065018.png)

选择你需要导入的仓库，我们用刚才创建的testbook，然后标题可以自己取，下面的地址只能填英文，然后点创建

![Alt text](http://img.hksite.cn/2019-03-29-065022.png)

这个页面目前是在同步，等同步完毕后，就可以看到自己的书了。

![Alt text](./1460873464695.png)

可以点击read

![Alt text](http://img.hksite.cn/2019-03-29-065025.png)

可以发现里面就1页，就是我们刚才SUMMARY.md写的内容

![Alt text](http://img.hksite.cn/2019-03-29-065029.png)

# 增加内容
我们退回上一步，点edit
![Alt text](http://img.hksite.cn/2019-03-29-065034.png)

可以看到如下界面
![Alt text](http://img.hksite.cn/2019-03-29-065037.png)


我们点开![Alt text](./1460873711587.png)

可以同步显示编辑后的效果
![Alt text](http://img.hksite.cn/2019-03-29-065040.png)

现在我们就可以完成我们的书的录入了，在目录区点击右键，可以新建一个内容页

![Alt text](http://img.hksite.cn/2019-03-29-065043.png)

然后点击一下，会提示创建一个文件

![Alt text](http://img.hksite.cn/2019-03-29-065047.png)

然后你会发现在github上也多了一个文件，这就关联起来了。
![Alt text](http://img.hksite.cn/2019-03-29-065055.png)

我们再看SUMMARY里面多了一个“第一页”
![Alt text](http://img.hksite.cn/2019-03-29-065100.png)

我们双击一下，发现就是markdown的超链接，原来这就是gitbook的内容组织方式，通过超连接把内容和github上文件关联起来。
![Alt text](http://img.hksite.cn/2019-03-29-065104.png)

我们修改一下名字，然后把他缩进一个tab，保存看看有什么变化

![Alt text](http://img.hksite.cn/2019-03-29-065110.png)

可以发现目录结构变了，然后名字也变了

![Alt text](http://img.hksite.cn/2019-03-29-065113.png)

通过修改SUMMARY.md你可以轻松的组织你的书

# 书的框架
一般来说，你的书得有一个README.md和一个SUMMARY.md。
其中SUMMARY.md是最重要的，它代表了整个书的框架，也是我们主要需要修改的地方。

当你考虑好要写一本书，你可以先粗略的想好大概要分几部分，对这几部分弄一个文件夹。然后把对应的markdown文件放进去。

![Alt text](http://img.hksite.cn/2019-03-29-065117.png)

然后push到github上，然后再在gitbook上修改SUMAARY使得新加的文件得以跟gitbook关联


![Alt text](http://img.hksite.cn/2019-03-29-065121.png)

关于地址的获取有个小诀窍，就是点击文件，邮件，有个重命名，这里可以看到文件的完整地址，复制就好。
![Alt text](http://img.hksite.cn/2019-03-29-065126.png)



最后，可以在自己的书的主页点击read看看效果

![Alt text](http://img.hksite.cn/2019-03-29-065130.png)