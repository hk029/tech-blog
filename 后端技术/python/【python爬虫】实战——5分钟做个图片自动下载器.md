---
title: 【python爬虫】实战——5分钟做个图片自动下载器
date: 2016-04-19
categories: 
- 后端技术
- python
tags: 
- python
- 爬虫
init: 1
---
之前介绍了那么多基本知识，大家也估计手痒了。想要实际做个小东西来看看，毕竟：
>talk is cheap show me the code!

# 制作爬虫的基本步骤
顺便通过这个小例子，可以掌握一些有关制作爬虫的基本的步骤。

一般来说，制作一个爬虫需要分以下几个步骤：
1. 分析需求（对，需求分析非常重要，不要告诉我你老师没教你）
2. 分析网页源代码，配合F12（没有F12那么乱的网页源代码，你想看死我？）
3. 编写正则表达式或者XPath表达式（就是前面说的那个神器）
4. 正式编写python爬虫代码


# 效果
运行：
![Alt text](http://img.hksite.cn/2019-03-25-030040.jpg)

恩，让我输入关键词，让我想想，输入什么好呢？好像有点暴露爱好了。
![Alt text](http://img.hksite.cn/2019-03-25-030045.png)


回车

![Alt text](http://img.hksite.cn/2019-03-25-030047.png)


好像开始下载了！好赞！，我看看下载的图片，哇瞬间我感觉我又补充了好多表情包....

![Alt text](http://img.hksite.cn/2019-03-25-030049.png)



好了，差不多就是这么个东西。

# 需求分析
"我想要图片，我又不想上网搜“
"最好还能自动下载"
……

这就是需求，好了，我们开始分析需求，首先，搜索图片，最容易想到的就是爬百度图片的结果，好，那我们就上百度图片看看

![Alt text](http://img.hksite.cn/2019-03-25-030052.png)

基本就是这样，还挺漂亮的。

我们试着搜一个东西，我打一个暴字，出来一系列搜索结果，这说明什么....
![Alt text](./1460995848517.png)

随便找一个回车
![Alt text](./1460995882834.png)

好了，我们已经看到了很多图片了，如果我们能把这里面的图片都爬下来就好了。我们看见网址里有关键词信息

![Alt text](http://img.hksite.cn/2019-03-25-030101.png)

我们试着在网址直接换下关键词，跳转了有没有！

![Alt text](http://img.hksite.cn/2019-03-25-030104.png)

这样，可以通过这个网址查找特定的关键词的图片，所以理论上，我们可以不用打开网页就能搜索特定的图片了。下个问题就是如何实现自动下载，其实利用之前的知识，我们知道可以用request，获取图片的网址，然后把它爬下来，保存成.jpg就行了。

所以这个项目就应该可以完成了。

# 分析网页
好了，我们开始做下一步，分析网页源代码。这里 我先切换回传统页面，为什么这样做，因为目前百度图片采用的是瀑布流模式，动态加载图片，处理起来很麻烦，传统的翻页界面就好很多了。
![Alt text](http://img.hksite.cn/2019-03-25-030109.png)

这里还一个技巧，就是：能爬手机版就不要爬电脑版，因为手机版的代码很清晰，很容易获取需要的内容。

好了，切换回传统版本了，还是有页码的看的舒服。
![Alt text](http://img.hksite.cn/2019-03-25-030112.png)

我们点击右键，查看源代码
![Alt text](http://img.hksite.cn/2019-03-25-030119.png)

这都是什么鬼，怎么可能看清！！
![Alt text](http://img.hksite.cn/2019-03-25-030128.png)

这个时候，就要用F12了，开发者工具！我们回到上一页面，按F12，出来下面这个工具栏，我们需要用的就是左上角那个东西，一个是鼠标跟随，一个是切换手机版本，都对我们很有用。我们这里用第一
![Alt text](http://img.hksite.cn/2019-03-25-030125.png)

然后选择你想看源代码的地方，就可以发现，下面的代码区自动定位到了这个位置，是不是很NB!
![Alt text](http://img.hksite.cn/2019-03-25-030134.png)

我们复制这个地址
![Alt text](http://img.hksite.cn/2019-03-25-030140.png)

然后到刚才的乱七八糟的源代码里搜索一下，发现它的位置了！（小样！我还找不到你！)但是这里我们又疑惑了，这个图片怎么有这么多地址，到底用哪个呢？我们可以看到有thumbURL，middleURL，hoverURL，objURL
![Alt text](http://img.hksite.cn/2019-03-25-030148.png)

通过分析可以知道，前面两个是缩小的版本，hover是鼠标移动过后显示的版本，objURL应该是我们需要的，不信可以打开这几个网址看看，发现obj那个最大最清晰。
![Alt text](http://img.hksite.cn/2019-03-25-030150.png)

![Alt text](http://img.hksite.cn/2019-03-25-030153.png)


好了，找到了图片位置，我们就开始分析它的代码。我看看是不是所有的objURL全是图片

![Alt text](http://img.hksite.cn/2019-03-25-030201.png)


貌似都是以.jpg格式结尾的，那应该跑不了了，我们可以看到搜索出61条，说明应该有61个图片

# 编写正则表达式
通过前面的学习，写出如下的一条正则表达式不难把？

	pic_url = re.findall('"objURL":"(.*?)",',html,re.S)

# 编写爬虫代码
好了，正式开始编写爬虫代码了。这里我们就用了2个包，一个是正则，一个是requests包，之前也介绍过了，没看的回去看！
```python
#-*- coding:utf-8 -*-
import re
import requests
```
然后我们把刚才的网址粘过来，传入requests，然后把正则表达式写好，
```python
url = 'http://image.baidu.com/search/flip?tn=baiduimage&ipn=r&ct=201326592&cl=2&lm=-1&st=-1&fm=result&fr=&sf=1&fmq=1460997499750_R&pv=&ic=0&nc=1&z=&se=1&showtab=0&fb=0&width=&height=&face=0&istype=2&ie=utf-8&word=%E5%B0%8F%E9%BB%84%E4%BA%BA'



html = requests.get(url).text
pic_url = re.findall('"objURL":"(.*?)",',html,re.S)

```

理论有很多图片，所以要循环，我们打印出结果来看看，然后用request获取网址，这里由于有些图片可能存在网址打不开的情况，加个5秒超时控制。
```python
pic_url = re.findall('"objURL":"(.*?)",',html,re.S)
i = 0
for each in pic_url:
    print each
    try:
        pic= requests.get(each, timeout=10)
    except requests.exceptions.ConnectionError:
        print '【错误】当前图片无法下载'
        continue
```

好了，再就是把网址保存下来，我们在事先在当前目录建立一个picture目录，把图片都放进去，命名的时候，用数字命名把
```
    string = 'pictures\\'+str(i) + '.jpg'
    fp = open(string,'wb')
    fp.write(pic.content)
    fp.close()
    i += 1
```

整个代码就是这样：

```
#-*- coding:utf-8 -*-
import re
import requests

url = 'http://image.baidu.com/search/flip?tn=baiduimage&ipn=r&ct=201326592&cl=2&lm=-1&st=-1&fm=result&fr=&sf=1&fmq=1460997499750_R&pv=&ic=0&nc=1&z=&se=1&showtab=0&fb=0&width=&height=&face=0&istype=2&ie=utf-8&word=%E5%B0%8F%E9%BB%84%E4%BA%BA'


html = requests.get(url).text
pic_url = re.findall('"objURL":"(.*?)",',html,re.S)
i = 0
for each in pic_url:
    print each
    try:
        pic= requests.get(each, timeout=10)
    except requests.exceptions.ConnectionError:
        print '【错误】当前图片无法下载'
        continue
    string = 'pictures\\'+str(i) + '.jpg'
    fp = open(string,'wb')
    fp.write(pic.content)
    fp.close()
    i += 1
```
我们运行一下，看效果（什么你说这是什么IDE感觉很炫！？赶紧去装Pycharm，看这个文章！）!
![Alt text](http://img.hksite.cn/2019-03-25-030205.png)

好了我们下载了58个图片，咦刚才不是应该是61个吗？
![Alt text](http://img.hksite.cn/2019-03-25-030210.png)

我们看，运行中出现了有一些图片下载不了
![Alt text](http://img.hksite.cn/2019-03-25-030215.png)

我们还看到有图片没显示出来，打开网址看，发现确实没了。
![Alt text](http://img.hksite.cn/2019-03-25-030221.png)


所以，百度有些图片它缓存到了自己的机器上，所以你还能看见，但是实际连接已经失效


好了，现在自动下载问题解决了，那根据关键词搜索图片呢？只要改url就行了，我这里把代码写下来了
```python

    word = raw_input("Input key word: ")
    url = 'http://image.baidu.com/search/flip?tn=baiduimage&ie=utf-8&word='+word+'&ct=201326592&v=flip'
    result = requests.get(url)
```

好了，享受你第一个图片下载爬虫吧！！