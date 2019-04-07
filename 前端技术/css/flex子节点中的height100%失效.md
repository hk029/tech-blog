---
title: flex子节点中的height:100%失效
date: 2018-10-29
categories: 
- 前端技术
- css
tags: 
- css
- flex
init: 1
---
> 注：7x新版本的chrome已经没有这个问题，但是safari 12.0.3还可以复现

## 起因

在今天做项目的时候，遇到了这个问题，我发现在一个`flex:auto`的节点内使用`height:100%`没有效果。（你可以[点击这里](<https://codepen.io/voidsky/pen/OoRWKY>)查看例子）

![image-20190402193111909](http://img.hksite.cn/2019-04-02-113112.png)

发现网上这样的问题很多，国内大多都写的是：把子节点设置成`position:abolute`就行了，具体原因也没多讲。

在github上发现有人提了类似的问题：https://github.com/philipwalton/flexbugs/issues/197

![20180829153554320079437.png](http://img.hksite.cn/20180829153554320079437.png)

但是神奇的是，这个问题，在firefox上并不存在。（以下是在firefox上运行结果）

![20180903153598111790710.png](http://img.hksite.cn/20180903153598111790710.png)

因此有很多人在chromium上提了这个bug，认为这是一个chrome的bug。![20180829153554331665068.png](http://img.hksite.cn/20180829153554331665068.png)

![20180829153554324944318.png](http://img.hksite.cn/20180829153554324944318.png)

## 问题探源

在群里的小伙伴的积极帮助下，我们发现：

1. 只要`flex:auto`的容器同时设置一个`height: 0`（其实不止是0，任意值只要是非`auto`字段就行） 孩子就能自适应高度（不是0哦）
2. 把`flex:auto`改成`flex:1`，问题也会迎刃而解

所以这看起来更像一个玄学问题？那这到底是浏览器的bug，还是我们自己的用法出了问题？

> 神奇不？你肯定想知道为什么，于是我们继续刨根问底往下看

## height:0为什么可以？

首先，必须要解决的问题是，height:100%是如何生效的，网上相关的博文很多，大家都表达了一个观点：

> 如果你需要对一个子元素设置高度100%，你需要给这个元素的所有父元素的高度设定一个有效值。

就类似下面这样

```html
<html style="height: 100%;">
  <body style="height: 100%;">
    <div style="height: 100%;">
      <p>
        这样这个div的高度就会100%了
      </p>
    </div>
  </body>
</html>
```

其实这很好理解，因为根据BFS布局，所有的块元素是从上往下排列的（类似俄罗斯方块），一个包含块如果不指定高度，它作为容器高度是**`可变的`**可以一直根据它的内容增加。（很多时候这也是符合我们预期的），这种情况下，你获取的100%又从哪知道呢？

![20180904153603207590246.png](http://img.hksite.cn/20180904153603207590246.png)

但是在上例我们flex布局中，全局高度已经指定，上下高度也指定了，理论上，中间自适应的高度**理论上**也能很明确的计算出来了。这种情况下为什么我们`height:100%` 不生效呢？

这时候就要查看官方文档了，根据css2.1文档中有关[height一节](https://www.w3.org/TR/CSS2/visudet.html#propdef-height)的介绍：

> 如果指定了百分比高度，那么它会根据其包含块的高度计算，但是，如果其高度没有被显式设置，那么它的值会被设置成`auto`

![20180904153603061124849.png](http://img.hksite.cn/20180904153603061124849.png)

也就是说，哪怕flex布局中，我们包含块的高度计算出来了，但是由于一开始没有被显式制定，孩子的height已经被设置成auto了，跟包含块的联系**切断了**。但是如果你给包含块设置一个``height:0` ，就显式指定了包含块和孩子的高度依赖关系。这时候，孩子就能根据包含块的高度做自适应了。

## flex:1 为什么可以

`flex:auto` 和  `flex: 1`唯一的区别就是`flex:1` 把`flex-basis`设置成`0%`，就是这么一个很小的区别，就直接影响了整体的布局。

![2018082915355444399973.png](http://img.hksite.cn/2018082915355444399973.png)

![20180829153554446967697.png](http://img.hksite.cn/20180829153554446967697.png)

flex的文档中也有相关的描述，设置flex:auto 或者 flex: ， 都会创建一个

![20180829153554491088054.png](http://img.hksite.cn/20180829153554491088054.png)

那为什么设置`flex-basis`就可以了呢？根据w3c文档中的说明，flex-basis定义当前元素在主轴上的尺寸。所以设置该选项就等同于设置了`height`/ `width`为一个具体的值。

https://www.w3.org/TR/2017/CR-css-flexbox-1-20171019/#main-size

	通过设置flex:1就行了。那应该就是flex-basis的问题了，[ flex:auto ]的时候，basis是auto，flex-basis是控制size的，在direction为column的时候，basis控制height，此时[ height:auto ]，所以无法通过100%继承，如果把flex设为1，此时等于把basis设置成0%，所以等于显式设置了height为0%，此时孩子就可以通过100%继承。

以上是我的理解。欢迎讨论。以前都没重视过flex这些取值。naive了





## 解决方案：

知道了原因，解决起来也就简单了：

### 方案一：flex:1

设置`flex:1`，flex:1 会把flex-basis设置成0，（其实把flex-basis设置成任何值都可以0）

```css
.parent {
	flex: 1;
}
.child {
	height: 100%;   
}
```

### 方案二：height:0

设置`flex:auto`，然后设置`height:0`

```css
.parent {
	flex: auto;
  height: 0;
}
.child {
	height: 100%;   
}
```

### 方案三：absolute

设置`flex:auto`，然后设置孩子为`position: absolute`

```css
.parent {
	flex: auto;
  position: relative;
}
.child {
	height: 100%;   
  position: absolute;
}
```