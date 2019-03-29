---
title: Vim自带的Esc替代方案
date: 2016-09-28
categories: 
- 技术生活
- 工具
tags: 
- vim
---


<h1>vim离开模式</h1>

很多人只知道vim的离开是用<code>Esc</code>，但是很多人使用了也会发现，这是一个挺反人类的按键，因为目前的键盘<code>Esc</code>都是设置在最左上角，和原来的键盘区别很大，而恰好<code>Esc</code>又是vim里使用频率很高的按键，这无疑违背了vim的高效性。vim按键设计很程度上就是希望操作者的手可以完全不离开键盘( 比如用hjkl取代上下左右键 )，但是很少有人能按<code>Esc</code>而手不离开键盘（长指猿除外）

所以，vim内嵌几个离开模式的映射。

<h2>Ctrl-C</h2>

对，就是复制这个快捷键，在手册里，这个快捷键被映射成了Esc

<h2>CTRL-O</h2>

这个快捷键牛逼了，执行一次操作后自动回到<code>insert</code>模式，这个模式在很多情况下，比CTRL-C还有用，而且也是被很推荐使用的一个快捷键。因为很多时候，我们进入普通模式只是为了简单的处理一条命令，又要切换回Insert模式。

比如输入代码的时候，我们需要移动一下光标到括号后面，输入一个;

<pre><code>CTRL-O a
</code></pre>

<img src="http://img.hksite.cn/1475046510295" alt="your text" />

或者，有一个单词打错了，就像回退一格然后输入

<pre><code>CTRL-O j
</code></pre>

<img src="http://img.hksite.cn/1475046538207" alt="your text" />

或者，你可以先输入一条命令，然后命令结束自动跳到输入模式

<pre><code>CTRL-O :command
</code></pre>

<img src="http://img.hksite.cn/1475046547474" alt="your text" />
    