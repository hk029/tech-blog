---
title: vertical-align导致inline-block塌陷问题
date: 2017-03-11
categories: 
- 前端技术
- css
tags: 
- css
- 布局排版
init: 1
---

有很多喜欢用inline-block做行内排版对齐，这本来是一个很不错的方式，但是今天在做一个布局的时候，发现一个奇怪的现象，就是当块中输入文字后，整个布局就发生了诡异的一幕：第一个方块下移了，而且怎么都回不来。

![your text](<http://img.hksite.cn/1489145950951> "")

其实仔细观察还能看到，下移的位置刚好是让文字的底部和别的块的底部对齐了。其实，这个问题产生的原因，就是因为inline元素的对齐机制。在默认情况下，inline元素`vertical-align`依据文字的`baseline`(画红线的地方）对齐的，没有文字按inline-block的下边缘对齐。

![your text](<http://img.hksite.cn/1489146165288> "")

所以，我们希望inline-block都按顶端或底端对齐，可以把`vertical-align`改成`top`或`bottom`。

![your text](<http://img.hksite.cn/1489146431518> "")

![your text](<http://img.hksite.cn/1489146391574> "")