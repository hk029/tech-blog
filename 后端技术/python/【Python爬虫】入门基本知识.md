---
title: 【python爬虫】入门基本知识
date: 2016-04-14
tags: 
- python
- 爬虫
---



# 写在开头

这阵子需要用爬虫做点事情，于是系统的学习了一下python爬虫，觉得还挺有意思的，比我想象中的能干更多的事情，这里记录下学习的经历。

网上有关爬虫的资料特别多，写的都挺复杂的，我这里不打算讲什么大道理，因为其实爬虫挺好理解的。就是下面一个流程：

网页网页源代码正则表达式需要的内容

爬虫的功能就是把网页源代码想办法爬下来，然后分析出需要的内容。总结起来就是2个部分：
1. 爬
2. 提取

所以，整个爬虫需要掌握的技能，就是如何高效的爬，如何快速的分析提取所需要的内容。

# 如何爬？

## Requests

说实话，之前为了找爬虫的教程，走了挺多弯路的，因为现在很多教程刚上来就介绍`urllib`，`urllib2`这两个python自带的有关网页的包，所以刚开始我的单线程爬虫实现也都是基于urllib的，不仅代码多，而且效率还低。实际上，目前来说，这两个已经很过时了，目前用的比较多的是`requests`这个第三方包（这里我也是偶然间发现极客学院有关爬虫的视频，让我少走那么多弯路，这里我就不说是什么视频了，以免有广告的嫌疑，大家有兴趣的可以自己去搜）。
正如requests的官方网页说的：

> Requests: HTTP for Humans

它目前应该是python下最好的Http库了。它还有很多别的特性：

> Requests 使用的是 urllib3，继承了urllib2的所有特性。Requests支持HTTP连接保持和连接池，支持使用cookie保持会话，支持文件上传，支持自动确定响应内容的编码，支持国际化的 URL 和 POST 数据自动编码。

上面介绍的是单线程爬虫，然后，如果要提高爬的效率，并行化肯定必不可少，那么scrapy就可以解决你的问题。然后还有js动态加载的问题。那些我以后也会慢慢加上来。

## Requests安装

    pip install requests


所有的python第三方包的安装都可以用pip，如果cmd中无法输入pip命令，请把`C:\Python27\Script`s加入PATH环境变量。

**注：**这里不推荐使用easy_install 因为这个只管安装，不管卸载。

## Requests使用

基本知道一个`requests.get()`和`requests.post()`就行了。

同样它还有

- requests.head()
- requests.delete()

不过用的不多。需要的时候，查手册就好了。这里有个文档写requests写的挺全面的。可以看看：[requests快速上手](http://cn.python-requests.org/zh_CN/latest/user/quickstart.html)

requests的返回值可以有多种形式输出，最常用的是`.text`和`.content`，前者输出**unicode**，后者输出**二进制**

```python
import requests  
 url = 'http://www.baidu.com'
 html = requests.get(url)
 print html.text
```

输出：

```html
<!DOCTYPE html><!--STATUS OK--><html><head><metahttp-equiv="content-type"content="text/html;charset=utf-8"><metahttp-equiv="X-UA-Compatible"content="IE=Edge"><metacontent="always"name="referrer"><metaname="theme-color"content="#2932e1"><linkrel="shortcut icon"href="/favicon.ico"type="image/x-icon" /><linkrel="search"type="application/opensearchdescription+xml"href="/content-search.xml"title="百度搜索" /><linkrel="icon"sizes="any"maskhref="//www.baidu.com/img/baidu.svg"><linkrel="dns-prefetch"href="//s1.bdstatic.com"/><linkrel="dns-prefetch"href="//t1.baidu.com"/><linkrel="dns-prefetch"href="//t2.baidu.com"/><linkrel="dns-prefetch"href="//t3.baidu.com"/><linkrel="dns-prefetch"href="//t10.baidu.com"/><linkrel="dns-prefetch"href="//t11.baidu.com"/><linkrel="dns-prefetch"href="//t12.baidu.com"/><linkrel="dns-prefetch"href="//b1.bdstatic.com"/><title>百度一下，你就知道</title>
……
……
```

# **如何提取？**

## **正则表达式**

正则表达式是一个大头！很多也都听过正则表达式，第一印象就是记不住，但是其实也不用特别记忆，因为在爬虫里，用的最多的基本就一个

    (.*?)


> ( ) ：表示这个内容是我们需要提取的
> .* ：表示匹配任意字符0到n次
> ？：表示非贪心，找对第一个就停下来

我来解释下为什么在爬虫里只要这个pattern就行了。
在html网页源代码中，我们需要找的内容一般都是被某些标签包围的，如果我们能保证找到我们需要的内容左右的标签（并且他们是独一无二的）那么我们很容易写出一个正则表达式：

    <XXX>(.*?)</XXX>


把其中的内容提取出来

## **python正则模块使用**

python的正则模块是re，主要用的函数是以下几个：（`re.S`的意思是让”.”可以匹配换行符，不然有些标签头和尾是分几行的，就会匹配失败）

- **主力部队**，把所有满足正则的内容提取出来，用于匹配满足某个条件的大量我们需要的内容。（比如所有的图片，所有的网址，所有的回复，所有的链接……）。它在网页提取中占了主要地位，工作量大，任务重，所以是主力部队。
```
    findall(pattern,str,re.S)
```


- **狙击手，**用来匹配第一个找到的元素，它的目标目的就是找到我们明显知道只有一个的元素比如标题什么的，一旦找到就结束，所以它的执行速度很快。它的目标明确，效率高，所以是狙击手的角色。
```
    search(pattern,str,re.S)
```

- **后勤，**它的功能是替换，一般用于替换一个网页地址中的关键词，替换页码等。它看似不重要，但是往往能在很多方面给我们提供便利，所以是后勤。注意：正则有时候一步不能完成我们需要的功能，可能需要进行几步操作，这时候，我们一般先提取大的部分，在从大部分里面提取我们需要的部分

```
    sub(pattern,str,replace)
```

我们看个很简单的例子：

```python
import re

#假设下面是一个源码，我想保存里面所有的链接
text = '<a href = "www.baidu.com">....'
urls = re.findall('<a href = (.*?)>',text,re.S)
for each in urls:
    print each
    
#假设我需要爬取当前网页的头部
html = '''
<html>
<title>爬虫的基本知识</title>
<body>
……
</body>
</html>
'''
print re.search('<title>(.*?)</title>',html,re.S).group(1)
#这里group(1)表示第一个括号的内容，如果正则里面有多个括号，这里可以通过group(i)返回第i个空格里的内容#假设下面是一个贴吧的帖子地址，有很多页，每一页就是靠后面的pn=几来区分的，我们输出前10页的网址
Pages = 'http://tieba.baidu.com/p/4342201077?pn=1'for i in range(10): 
    print re.sub('pn=\d','pn=%d'%i,Pages)
```


输出：

```html
"www.baidu.com"
爬虫的基本知识
http://tieba.baidu.com/p/4342201077?pn=0http://tieba.baidu.com/p/4342201077?pn=1http://tieba.baidu.com/p/4342201077?pn=2http://tieba.baidu.com/p/4342201077?pn=3http://tieba.baidu.com/p/4342201077?pn=4http://tieba.baidu.com/p/4342201077?pn=5http://tieba.baidu.com/p/4342201077?pn=6http://tieba.baidu.com/p/4342201077?pn=7http://tieba.baidu.com/p/4342201077?pn=8http://tieba.baidu.com/p/4342201077?pn=9
```

## **XPath**

如果说正则表达式就已经让你觉得很神奇了，那XPath真是要吓死你了。这真是个神器，它让提取信息网页信息变得更加轻松。XPath是一个树型的结构，比较符合“html”的层次结构。

> XPath即为XML路径语言，它是一种用来确定XML（标准通用标记语言的子集）文档中某部分位置的语言。XPath基于XML的树状结构，提供在数据结构树中找寻节点的能力。起初 XPath 的提出的初衷是将其作为一个通用的、介于XPointer与XSLT间的语法模型。但是 XPath 很快的被开发者采用来当作小型查询语言。

我觉得视频中老师的解释超精彩：如果你提取信息就像让你找一栋建筑，那么正则就是告诉你建筑左边是什么，右边是什么，但是全国可能有很多都满足条件的，你找起来还是不方便。

```
    红房子(.*?)绿房子
```

XPath就是告诉你，这个建筑在**北京市——海淀区——中关村——15号街——那栋黄色的建筑**，你可以马上找到对应的建筑。如果一个地名只有一个地方有，那么你更可以简化成“中关村——15号街”

```
    //北京市/海淀区/中关村/15号街[@房子颜色=黄色]/text()
    //中关村/15号[@房子颜色=黄色]/text()
```

也许在这里你还没能体会到他们之间的差别，但是相信我，当你遇到复杂的html分析的时候，你会发现它的厉害之处的。比如下面的例子，我想把Hello，my world!打印出来用正则需要考虑一下吧？但是用XPath就简单很多

    <divid="class">Hello，
        <fontcolor=red>my</font><fontcolor=green>world!</font><div>

### **XPath语法**

XPath你只需要知道这些语法

> // 根节点
> / 下一层路径
> [@XX=xx] 特定的标签
>
> /text() 以文本返回
> /@para 返回参数
>
> string(.) 当前层的所有内容作为一个字符串输出
> start-with(str) 所有以这个str开头的标签

下面是一个简单的例子

```python
from lxml import etree
html=
'''
<div id="test1">content1</div>
<div id="test2">content2</div>
<div id="test3">content3</div>
'''

selector = etree.HTML(html)
content = selector.XPath('//div[start-with(@id,"test")]/text()')
for each in content:
    print each

html1=
'''
<div id="class">Hello,
    <font color=red>my</font>
    <font color=green>world!</font>
<div>
'''

selector = etree.HTML(html)
tmp = selector.XPath('//div[@id="class"]')[0]
info = tmp.XPath('string(.)')
content2 = info.replace('\n','')
print content2
```

输出：

    content 1
    content 2
    content 3
    Hello,        my        world!  