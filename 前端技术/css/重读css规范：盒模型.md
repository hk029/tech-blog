---
title: 重读css规范：盒模型
date: 2019-04-07
categories: 
- 前端技术
- css
tags: 
- css
init: 1
---
# 重读css规范：盒模型

> 本文是重读css规范——盒模型部分的笔记，增加了一些个人见解和扩展。
>
> 规范文档地址：<https://www.w3.org/TR/CSS22/box.html>

## 盒模型概念

首先，让我们聊聊什么是盒模型，官方的说明：*CSS盒模型描述了一个为[文档树](https://www.w3.org/TR/CSS22/conform.html#doctree)中的元素生成的并根据[视觉格式化模型](<https://www.w3.org/TR/CSS22/visuren.html>)进行布局的矩形框*。

> 注意：只有盒（块元素）满足以下的的模型（行内元素不会生成新的内容块），下一章会介绍怎么生成盒。

这张图就是盒模型的例子，其中content表示实际的内容（文字，图片……）然后，周围会被padding，border，margin包围。

![image-20190403104614596](http://img.hksite.cn/2019-04-03-024615.png)

这张图泾渭分明的几条边界将盒子分成了不同的区域

- **content box**：内容区域
- padding box：内容+padding
- **Border box**：内容+padding+border
- Margin box：内容+padding+border+margin

其中画了红色和蓝色的区域比较重要，这是因为这就是css3属性`box-sizing`的可选值。（ie6以下是border-box，现代浏览器默认是content-box），这决定了你设置width是设置的整个蓝色部分（box-sizing:content-box）的宽度还是红色部分（box-sizing:border-box）。

很多有经验的开发者[认为在任何时候都应该将box-sizing设置为`border-box`](https://css-tricks.com/international-box-sizing-awareness-day/)，大多数情况下这使得我们更容易的去设定一个元素的宽高。例如你设置了宽度为50%，那么你不需要考虑padding，border，这个元素就是50%，更方便你去布局。

![](http://img.hksite.cn/2019-04-03-032252.jpg)

## 一个例子

官方给了一个例子，这个例子展示了盒模型的很多事实：

```css
 UL { 
        background: yellow; 
        margin: 12px 12px 12px 12px;
        padding: 3px 3px 3px 3px;
                                     /* No borders set */
      }
      LI { 
        color: white;                /* text color is white */ 
        background: blue;            /* Content, padding will be blue */
        margin: 12px 12px 12px 12px;
        padding: 12px 0px 12px 12px; /* Note 0px padding right */
        list-style: none             /* no glyphs before a list item */
                                     /* No borders set */
      }
      LI.withborder {
        border-style: dashed;
        border-width: medium;        /* sets border width on all sides */
        border-color: lime;
      }
```

![your text](http://img.hksite.cn/1499743337397)

事实：

1. 每个盒的内容宽度是**自上而下**计算的，每个LI的**包含块**由UL元素建立。
2. LI框的`margin`是透明的——`margin`总是透明的，所以UL填充和内容区域的背景色（黄色）会通过它们。
3. 垂直高度的margin会折叠

下面细致介绍一下margin，padding，border

## margin

- margin如果设置值为百分比，则是依据生成盒子的**包含块宽度**，**宽度！**，**宽度！**，哪怕是`margin-top`和`margin-bottom`。这一条请记熟！！（其实也可以理解，盒的宽度是一定的，但是高度经常是不可控的。）

> The percentage is calculated with respect to the *width* of the generated box's [containing block](https://www.w3.org/TR/CSS22/visuren.html#containing-block).Note that this is true for ['margin-top'](https://www.w3.org/TR/CSS22/box.html#propdef-margin-top) and ['margin-bottom'](https://www.w3.org/TR/CSS22/box.html#propdef-margin-bottom) as well. If the containing block's width depends on this element, then the resulting layout is undefined in CSS 2.2.

- margin可以取**负值**（布局常用，比如双飞翼和圣杯布局，或是小范围位移）
- 如果margin取auto，则要看[计算公式](https://www.w3.org/TR/CSS2/visudet.html#Computing_widths_and_margins)
- margin初始为0
- 只有`margin-top`和`margin-bottom`对**非替换行内元素无效**

![your text](http://img.hksite.cn/1499743907442)

## ★collapsing margins

在CSS中，两个或多个盒子（**或可能不是兄弟姐妹**）的相邻边可以组合成一个边。这种组合方式被称为collapse(塌陷)，由此产生的合并margin称为`collapsed margin`

特殊情况：

- 水平margin不会塌陷

- root element's box 不会塌陷

如果有`clearance`的元素的顶部和底部margin相邻，则其边距与后面的兄弟姐妹的相邻**边距折叠**，但由此产生的边距**不会与父块的底部边距折叠**。



### 相邻规则

两个margin是**相邻的**，当且仅当：

1. 都属于**流内**（in-flow）[**块级盒**](http://www.ayqy.net/doc/css2-1/visuren.html#block-boxes)，处于**同一个**[块格式化上下文**BFC**](http://www.ayqy.net/doc/css2-1/visuren.html#block-formatting)
2. **没有行盒** （line box），**没有空隙（clearance）**，**没有padding**并且**没有border把它们隔开**（注意，因此[某些0高度行盒](http://www.ayqy.net/doc/css2-1/visuren.html#phantom-line-box)（见[9.4.2](http://www.ayqy.net/doc/css2-1/visuren.html#inline-formatting)）会被忽略）
3. 都属于竖直相邻盒边（vertically-adjacent box edges），即来自下列某一对：
   - 一个**盒的top margin**和它的第一个流内（in-flow）**孩子的top margin**
   - 一个盒的**bottom margin**和它的下一个流内后面的**兄弟的top margin**
   - **最后**一个流内**孩子的bottom margin**和它的**父级的bottom margin**，**如果父级的高度的计算值为'auto'**
   - 一个盒的top和bottom margin**自己折叠** ： 该盒没有建立一个新的块格式化上下文并且['min-height'](http://www.ayqy.net/doc/css2-1/visudet.html#propdef-min-height)的**计算值为0**，['height'](http://www.ayqy.net/doc/css2-1/visudet.html#propdef-height)的计**算值为0或者'auto'**，并且没有流内孩子。

如果一个margin的任何部分margin与另一个margin相邻的话，就认为它与那个margin相邻，是合并（collapsed）margin

**注意：**相邻margin可以通过**非兄弟或者祖先关系**的元素生成

```css
    .parent-box {
            margin: 100px;
            padding: 0px;
            width: 500px;
            background: pink;
        }

        .child-box {
            margin: 10%;
            padding: 1%;
            width: 200px;
            background: greenyellow;
        }
```

此时明显，父亲和孩子的上下margin都重合了，要想打破这写重合，就打破相邻规则

![your text](http://img.hksite.cn/1499745383958)

给父亲加1px padding

![your text](http://img.hksite.cn/1499745499468)

给父亲加border

![your text](http://img.hksite.cn/1499745519630)

加入一个文字行盒（底部没有元素，所以margin-bottom依然重叠）

![your text](http://img.hksite.cn/1499745570551)



### 规则拆开

- 一个[浮动的](http://www.ayqy.net/doc/css2-1/visuren.html#floats)盒（**非流内**）与任何其它盒之间的margin不会合并（甚至一个浮动盒与它的流内子级之间也不会）
- 建立了新的块格式化上下文的元素（例如，浮动盒与['overflow'](http://www.ayqy.net/doc/css2-1/visufx.html#propdef-overflow)不为'visible'的元素）的margin不会与它们的流内子级合并（**非同一BFC**）
- [绝对定位的](http://www.ayqy.net/doc/css2-1/visuren.html#absolutely-positioned)盒 （**非流内**）的margin不会合并（甚至与它们的流内子级也不会）
- 内联盒的margin不会合并（**非块级元素**）（甚至与它们的流内子级也不会）
- 一个流内块级元素的bottom margin总会与它的下一个流内块级兄弟的top margin合并，**除非兄弟有空隙**
- 一个流内块级元素的top margin会与它的第一个流内块级子级的top margin合并，如果该元素**没有top border**，没有**top padding**并且该子级**没有空隙**
- **一个height为'auto'并且min-height为0**的流内块级盒的bottom margin会与它的最后一个流内块级子级的bottom margin合并，如果该盒没有bottom padding并且没有bottom border并且子级的bottom margin不与有空隙的top margin合并
- 盒自身的margin也会合并，如果['min-height'](http://www.ayqy.net/doc/css2-1/visudet.html#propdef-min-height)属性为0，并且既没有top或者bottom border也没有top或者bottom padding，并且其['height'](http://www.ayqy.net/doc/css2-1/visudet.html#propdef-height)为0或者'auto'，并且不含行盒，并且其所有流内子级的margin（如果有的话）都合并了

当两个或多个边距折叠，所得到的边距宽度是**折叠边距宽度的最大值**。

在负边距的情况下，负相邻边距的绝对值的最大值从正相邻边距的最大值中扣除。如果没有正的边距，则将相邻边距的绝对值的最大值从零中扣除。



## padding

![your text](http://img.hksite.cn/1499751384456)



- **padding 可以用于除table外所有的元素**
- padding的百分比计算和margin一样，都**只与父亲的width有关**
- 初始值为0

## border

![your text](http://img.hksite.cn/1499751973395)

- 边框属性指定框边框的宽度、颜色和样式。

- ★这些属性适用于**所有元素**。

- 边框宽度可以为：thin，medium，thick，长度（**不能用百分比**），**默认medium**

- 边框**颜色默认值**为`color`值，可以取transparent，此时border透明，但是有宽度

- 边框样式：none，hidden，dotted，dashed，solid，double（两条实线。两条实线及它们之间的空隙之和等于'border-width'的值），groove( 3D凹槽 )，ridge（3D凸槽），inset （嵌进画布，无3D），outset （从画布里出来了），边框**没有默认样式，必须指定**

- border值为'groove'，'ridge'，'inset'和'outset'的边框颜色取决于元素的border color属性，但UA可能会选用它们自己的算法来计算应用的实际颜色（可能有渐变）

- ★border设置的时候，**最少需要设置一个样式**

 

在这个例子中，边界将是一条实心黑线。

```css
p { 
  color: black; 
  background: white; 
  border: solid;
}
```

因为，在一定程度上，属性具有重叠的功能，指定规则的顺序很重要

考虑本例：

```
blockquote {
  border: solid red;
  border-left: double;
  color: black;
}
```

上例中，**left border的颜色是black**，而其它border是red。这是因为border-left同时设置了width，style和color。因为color值没有通过border-left属性给出，它将从color属性取。color属性确实是在border-left属性之后设置的



## 双向环境（bidirectional context）中内联元素的盒模型

对于每一个行盒（line box），UA必须为每个元素生成**内联盒**（inline box）并按照可视顺序（不是逻辑顺序）渲染margin，border和padding

- 当元素的['direction'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-direction)属性为'**ltr**'时，元素所在的第一个行盒最左端生成的盒具有left margin，left border和left padding，并且元素所在的最后一个行盒最右端生成的盒具有right padding，right border和right margin
- 当元素的['direction'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-direction)属性为'**rtl**'时，元素所在的第一个行盒最右端生成的盒具有right padding，right border和right margin，并且元素所在的最后一个行盒最左端生成的盒具有left margin，left border和left padding





## 定位模式

css2.1中规定了3种定位模式：

- **常规流**（normal flow）：常规流中包含**块格式化**和**内联格式化**，和**相对定位**的盒子

- **浮动**：在浮动模型中，一个盒先根据常规流布局，然后从流中取出来尽可能地左移或右移。其它内容可能会沿着浮动（盒）的一侧排列（Content may flow along the side of a float）

- **绝对定位**：在绝对定位模型中，一个盒会从常规流中全部移除（它不会影响后面的兄弟）并根据包含块确定位置

 

如果一个元素是**浮动**的，**绝对定位**的或者是**根元素**，它就叫流外（out of flow）（元素）。如果一个元素不是流外的，就叫流内（in-flow）（元素）。

元素A的流是由**A和所有最近的流外祖先为A 的流内元素组成的集合**



![your text](http://img.hksite.cn/1499849522726)

- 不可继承，不可设置百分比

- 默认static

- 属性：

  - **static**：元素在常规流内，但是top等属性设置无效
  - **relative：**盒的位置是根据常规流中的位置计算的，然后盒相对于其常规位置**偏移(可以设置top等值了)**。当盒B为相对定位时，后面的盒的位置就**按照B没有偏移量**（参照B的位置）来计算。table类元素上'position:relative'的效果是undefined
  - **absolute：**盒的位置（及可能的大小）由['top'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-top)，['right'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-right)，['bottom'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-bottom)和['left'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-left)属性指定，这些属性指定了相对于盒的[包含块](http://www.ayqy.net/doc/css2-1/visuren.html#containing-block)的偏移量。绝对定位的盒脱离了常规流，意味着它们**不会影响后面兄弟的布局**，而且，虽然[绝对定位的](http://www.ayqy.net/doc/css2-1/visuren.html#absolutely-positioned)盒**有margin**，但它们不会与任何其它margin合并。
  - **fixed：**除了盒相对于某些参照（reference）是固定的（fixed）之外，盒的位置根据'absolute'模型来计算。如同'absolute'模型一样，盒的margin不会与任何其它margin合并。媒体类型为**handheld**，**projection**，**screen**，**tty**和**tv**的情况下，该盒相对于**视口（viewport）固定**，并且滚动时不会移动。媒体类型为**print**时，**该盒在每一页都会渲染**，并且**相对于页盒（page box）固定**，即使页面是通过一个视口看到的（例如，打印预览时）。这在排版（placing）方面很有用，**例如，每一页的底部都有一个签名**。fixed定位的盒比页区域大的部分会被裁剪。fixed定位盒在初始包含块中不可见的部分将不会被打印

  ```css
  @media screen { 
    h1#first { position: fixed } 
  }
  @media print { 
    h1#first { position: static }
  }
  ```

  用户代理可以把根元素的position视为'static'

### 位移属性

![your text](http://img.hksite.cn/1499850318215)



![your text](http://img.hksite.cn/1499850355645)

- 初始值为auto
- 百分比根据**包含块**的宽高
- 如果没有指定特定的值或百分比，默认都是auto
- 可以为负数（反向，right：-20%和left:20%一样）
- 该些属性指定了一个**绝对定位的盒**的top margin边离盒的**包含块**的各边有多远。对于**相对定位**的盒，偏移量参照该盒**自身的各边**

**auto：**：对于不可替换元素，该值的效果取决于那些相关的值也是'auto'的属性。不可替换元素的详细情况，见[绝对定位](http://www.ayqy.net/doc/css2-1/visuren.html#absolutely-positioned)的[width](http://www.ayqy.net/doc/css2-1/visudet.html#abs-non-replaced-width)和[height](http://www.ayqy.net/doc/css2-1/visudet.html#abs-non-replaced-height)章节。

对于可替换元素，**该值的影响只取决于替换内容的内在尺寸**。可替换元素的详细情况，见绝对定位的[width](http://www.ayqy.net/doc/css2-1/visudet.html#abs-replaced-width)和[height](http://www.ayqy.net/doc/css2-1/visudet.html#abs-replaced-height)章节





## 常规流

常规流中的盒属于一个**格式化上下文（FC）**，可能是**块**或是**内联**，但不能都是。

块级盒参与块格式化上下文（BFC）。内联级盒参与内联格式化上下文（IFC）

### 块格式化上下文

以下，会为它们的内容建立一个**新的块格式化上下文(BFC)**

- 浮动，绝对定位元素（脱离常规流）
- **非块盒**的块容器（inline-blocks, table-cells, and table-captions）
- 'overflow'不为'visible'的块盒（当该值已被传播到视口时除外（except when that value has been propagated to the viewport））
- 绝对定位元素
-

属性：

- **竖直**：在一个块格式化上下文中，盒在竖直方向从包含块的顶部开始一个接一个地放置。两个兄弟盒之间的竖直距离由`margin`属性决定。同一个块格式化上下文中的相邻块级盒之间的竖直margin会**合并**
- **横向**：在一个块格式化上下文中，每个盒的left外边挨着**包含块的left**边（对于从右向左的格式化，right边挨着）。即使存在浮动（尽管一个盒的*行盒*可能会因为浮动收缩），这也成立。除非该盒建立了一个新的块格式化上下文（这种情况下，该盒自身[*可能会因为浮动*变窄](http://www.ayqy.net/doc/css2-1/visuren.html#bfc-next-to-float)）

### 内联格式化上下文

- 在一个内联格式化上下文中，**盒是一个接一个水平放置的**，从包含块的顶部开始。


- 这些盒之间的**水平!!margin**，**border**和**padding**都有效。


- 盒可能以不同的方式竖直对齐：以它们的**底部**或者**顶部**对齐，或者以它们里面的**文本的基线对齐**。包含来自**同一行的盒**的矩形区域叫做**行盒**(line box)


- 一个**行盒**总是**足够高**，能够容纳它包含的所有盒。然而，它可能比它所包含的最高的盒还要高（例如，如果盒是以基线对齐）

- 当几个内联级盒在水平方向上不能共存于一个行盒时，它们会被分到**两个或多**个**垂直堆叠**的（vertically-stacked） 行盒里。因此，段落就是个行盒的垂直栈（vertical stack）。行盒**没有垂直间隔（所以垂直margin无效）**地堆放（除非在其它地方有特别说明）并且它们不会重叠

- 行盒的**宽度**由**包含块**和浮动情况决定，行盒的高度由[行高的计算](http://www.ayqy.net/doc/css2-1/visudet.html#line-height)小节给出的规则决定

- 一般来说，一个行盒的左右边挨着其**包含块**的左右边。然而，浮动盒可能会跑到包含块边与行盒边之间。因此，尽管同一个内联格式化上下文中的行盒一般都有相同的宽度（也就是包含块的宽度），如果可用的水平空间因为[浮动](http://www.ayqy.net/doc/css2-1/visuren.html#floats)而减少了的话，它们的**宽度就可能不同**。

- 同一个内联格式化上下文中的行盒**一般高度各不相同**（例如，一行可能含有一个高图片，而其它的只含文本）

- 当一行的内联级盒的总宽度小于它们所在的行盒的宽度时，它们在行盒里的**水平分布**由['text-align'](http://www.ayqy.net/doc/css2-1/text.html#propdef-text-align)属性决定。如果该属性值为'justify'，用户代理可能会拉伸内联盒（不包括inline-table和inline-block盒）里的空白和字（间距）

- 当盒B的高度小于它所在的行盒的高度时（**如果等于了也就不存在对齐了**），行盒中B的**竖直对齐**方式由['vertical-align'](http://www.ayqy.net/doc/css2-1/visudet.html#propdef-vertical-align)属性决定。

- 当一个**内联盒**超出一个**行盒**的宽度时，它会被**分成几个盒**，并且这些盒会**跨多行盒分布**。如果一个内联块无法分割（例如，如果该内联盒含有一个单个字符，或者特定语言的单词分隔规则不允许在该内联盒里分隔，或如果该内联盒受到了一个值为**nowrap**或者pre的**white-space**的影响），那么该内联盒会从**行盒溢出**，当一个内联盒被分割后，**margin**，**border**和**padding**在发生分割的地方（或者在**任何分割处**，如果有多处的话）**不会有可视化**效果

 

行盒其他：

- 行盒是因为盛放（hold）一个内联格式化上下文中的**内联级内容**时创建的。
- 不含文本、[保留空白符（preserved white space）](http://www.ayqy.net/doc/css2-1/text.html#white-space-prop)、margin，padding或者border非0的内联元素、其它[流内](http://www.ayqy.net/doc/css2-1/visuren.html#positioning-scheme)内容（例如，图片，inline block或者inline table），并且不以保留换行符（preserved newline）结束的行盒必须被当作一个0高度的行盒，为了确定它里面所有元素的位置，而其它时候（for any other purpose）必须当它不存在

有个内联盒构造的示例，下面的段落（HTML块级元素P创建的）含有匿名文本，穿插在EM和STRONG元素中：

```
<P>Several <EM>emphasized words</EM> appear
<STRONG>in this</STRONG> sentence, dear.</P>
```

P元素生成了一个含有5个内联盒的块盒，其中有3个是匿名的：

- Anonymous: "Several"
- EM: "emphasized words"
- Anonymous: "appear"
- STRONG: "in this"
- Anonymous: "sentence, dear."

为了格式化该段落，用户代理把5个盒排列（flows）在一个行盒里。本例中，P元素生成的盒为行盒建立了包含块。如果该包含块足够宽，所有内联盒都能放进一个行盒里：

```
 Several emphasized words appear in this sentence, dear.
```

如果不行，内联盒将被分割并跨行盒分布。之前的段落可能被分成下面这样：

```
Several emphasized words appear
in this sentence, dear.
```

```
Several emphasized  
words appear in this 
sentence, dear.
```

在之前的示例中，EM盒被分成了两个EM盒（叫它们"split1"和"split2"）。Margin，border，padding或者文本修饰（text decorations）不会在split1后面或split2前面产生可见的效果

考虑下例：

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN">
<HTML>
  <HEAD>
    <TITLE>Example of inline flow on several lines</TITLE>
    <STYLE type="text/css">
      EM {
        padding: 2px; 
        margin: 1em;
        border-width: medium;
        border-style: dashed;
        line-height: 2.4em;
      }
    </STYLE>
  </HEAD>
  <BODY>
    <P>Several <EM>emphasized words</EM> appear here.</P>
  </BODY>
</HTML>
```

取决于P的宽度，盒可能分布如下：

![Image illustrating the effect of line breaking on thedisplay of margins, borders, and padding.](http://www.ayqy.net/doc/css2-1/images/inline-layout.png)   

- 在"emphasized"前面和"words"后面插入了margin
- 在"emphasized"的前面，上面及下面，"words"的后面，上面及下面插入了padding，每种情况下都渲染了三个方向的虚线边框



## 相对定位

相对定位的盒保持它在常规流中的大小，包括换行和空格都会原样保留。[包含块](http://www.ayqy.net/doc/css2-1/visuren.html#containing-block)小节解释了一个相对布局盒会在什么时候建立一个新的包含块

对于相对定位的元素，'left'和'right'水平移动盒，不会改变其大小。'left'把盒向右移，而'right'把盒向左移动。因为盒没有被分割或者拉伸，所以'left'或'right'的应用值总是（保持）：left = -right

- 如果'left'和'right'都是'auto'（各自的初始值），应用值为'0'（即盒待在原位置）
- 如果'left'或'right'是'auto'，其应用值为负的'right'或'left'值（即盒向左移动'right'值）
- 如果'left'和'right'都不是'auto'，position就被过度约束（over-constrained）了，它们（'left'和'right'）其中有一个会被忽略。如果包含块的'direction'属性是'ltr'，那么'left'有效，'right'变成-'left'（负的'left'）。如果包含块的'direction'属性是'rtl'，那么'right'有效，'left'被忽略
- 'top'和'bottom'和left和right一致。只是如果同时设置了'top'和'bottom'，那么top生效

**示例** 下列3条规则是等价的：

```
div.a8 { position: relative; direction: ltr; left: -1em; right: auto }
div.a8 { position: relative; direction: ltr; left: auto; right: 1em }
div.a8 { position: relative; direction: ltr; left: -1em; right: 5em }
```

**注意** 脚本环境中，相对定位盒的动态移动能产生动画效果（另见['visibility'](http://www.ayqy.net/doc/css2-1/visufx.html#propdef-visibility)属性）。尽管相对定位可能被用于上标和下标的形式，但自动调整行高时并不会考虑其定位。更多信息请查看[行高的计算](http://www.ayqy.net/doc/css2-1/visudet.html#line-height)中的描述



## 浮动

float（盒）就是一个在当前行向左或向右移动的盒。



float（或者"floated"或者"floating"盒）最有意思的特性是其它内容**会沿着它的一侧排列**（可以通过['clear'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-clear)属性禁止这种行为）。



- 一个浮动盒会向左或向右移动，直到其外边（outer edge）**挨到包含块边或者另一个浮动盒的外边**。如果存在行盒，浮动盒的外top（边）会与当前行盒的top（边）对齐

- 如果没有足够的水平空间来浮动，它会向下移动，直到空间合适或者不会再出现其它浮动了

- ★因为浮动（盒）不在流内，在浮动盒之前或者之后创建的未定位的（non-positioned）块盒会竖直排列，就像浮动不存在一样。然而，接着（next to）浮动（盒）创建的**当前及后续行盒会进行必要的缩短**，为了给浮动（盒）的margin box让出空间

 

  如下面的abc的行盒本来是一行的宽度，现在就变成了紫色的区域

  ![your text](http://img.hksite.cn/1499868107033)

  ![your text](http://img.hksite.cn/1499867821463)

如果存在一个完全满足4个条件的的竖直位置：(a)在行盒top或者top以下，(b)在行盒bottom或者bottom以上，(c)在浮动（盒）的top margin边以下，并且(d)在浮动（盒）的bottom margin边以上，那么就说该行盒接着（next to）浮动（盒）

注意：**意思是0外高度（outer height）或者负外高度（的盒）的浮动不会缩短行盒**



- 如果一个**缩短的行盒小到无法容纳任何内容了**，那么该行盒**会向下移动**（并重新计算其宽度）直到有些内容能适应（它的空间）或者不会再出现其它浮动了。位于浮动（盒）之前的当前行盒里的任何内容都会**在浮动（盒）的另一侧的相同行重新排列（reflowed）**。换句话说，如果遇到了被放在一个向左浮动（的盒）之前的行盒里的内联级盒，它适应剩余的行盒空间，则该向左浮动（的盒）会被放在该行，**与行盒的top对齐**，然后该**行里的内联级盒会被移动到浮动（的盒）的右边**，对于rtl和向右浮动则正好相反（参考上面图片）



```html
p { width: 10em; border: solid aqua; }
span { float: left; width: 5em; height: 5em; border: solid blue; }

<p>
  <span> </span>
  Supercalifragilisticexpialidocious
</p>
```

效果可能像这样：

![Image illustrating the effect of an unbreakable piece of content  being reflowed to just after a float which left insufficient room next to it  for the content to fit.](http://www.ayqy.net/doc/css2-1/images/supercal.png)





- 一个表格，块级可替换元素或者常规流中建立了新的[块格式化上下文](http://www.ayqy.net/doc/css2-1/visuren.html#block-formatting)的元素（例如一个'overflow'不为'visible'的元素）**不能和与元素自身处于同一块格式化上下文中的任何浮动（盒）的margin box重叠**。如果必要的话，实现应该通过把它放在所有之前出现的浮动（盒）下方来清除（clear）该元素，如果空间充足的话，可以放得与浮动（盒）相邻。

如果把img设置成向左浮动 ，下面两种写法效果完全一样

```
 <P><IMG src=img.png alt="This image will illustrate floats">
       Some sample text that has no other...
```

```
  <P>Some sample text 
  <IMG src=img.png alt="This image will illustrate floats">
           that has no other...
```

![your text](http://img.hksite.cn/1499869723054)



因为浮动（盒）左边的内容被浮动（盒）替代了，所以（内容）会沿着其右侧重新排列（reflowed），并且p的margin和img的margin不重叠



浮动（盒）的内容会堆叠起来，就像浮动（盒）生成了新的层叠上下文（stacking contexts）一样，除了所有定位的元素和实际创建了新的层叠上下文并参与了浮动（盒）的父级堆叠上下文的元素。**一个浮动（盒）可以与常规流中的其它盒重叠**（例如，当一个常规流盒接着一个有负margin的浮动（盒）时），出现这种情况时，浮动（盒）会被渲染在未定位的（non-positioned）流内块之前，流内内联（盒）之后



![your text](http://img.hksite.cn/1499870311497)

如果设置了clear：

```
p { clear: left }
```

![your text](http://img.hksite.cn/1499870366200)



![your text](http://img.hksite.cn/1499870843465)

- 用户代理可以把根元素上的float当作'none'

### 浮动规则

1. 一个左浮动盒的left[外边（outer edge）](http://www.ayqy.net/doc/css2-1/box.html#outer-edge)不能位于其[包含块](http://www.ayqy.net/doc/css2-1/visuren.html#containing-block)的left边的左边。向右浮动的元素也有类似的规则
2. 如果当前盒是向左浮动的，并且在这之前源文档中还有元素生成了左浮动盒，那么对于每一个之前的盒，要么当前盒的left[外边](http://www.ayqy.net/doc/css2-1/box.html#outer-edge)在之前的盒的right[外边](http://www.ayqy.net/doc/css2-1/box.html#outer-edge)的右边，要么它的top要比之前的盒的bottom低。右浮动盒也有类似的规则
3. 一个左浮动盒的right[外边](http://www.ayqy.net/doc/css2-1/box.html#outer-edge)不能位于接着它的任意右浮动盒的left[外边](http://www.ayqy.net/doc/css2-1/box.html#outer-edge)的右边。右浮动盒也有类似的规则
4. 一个浮动盒的[外top（outer top）](http://www.ayqy.net/doc/css2-1/box.html#outer-edge)不能高于其[包含块](http://www.ayqy.net/doc/css2-1/visuren.html#containing-block)的top。当浮动（盒）出现在两个合并的margin之间时，浮动（盒）的定位就像它有一个空的[匿名块父级](http://www.ayqy.net/doc/css2-1/visuren.html#anonymous-block-level)存在于（当前）流一样。这个父级的位置通过margin合并章节中的[规则](http://www.ayqy.net/doc/css2-1/box.html#collapsed-through)来定义
5. 一个浮动盒的[外top](http://www.ayqy.net/doc/css2-1/box.html#outer-edge)不能高于源文档中任何在此之前的元素生成的[块盒](http://www.ayqy.net/doc/css2-1/visuren.html#block-boxes)或者[浮动盒](http://www.ayqy.net/doc/css2-1/visuren.html#floats)的外top
6. 一个元素的浮动盒的[外top](http://www.ayqy.net/doc/css2-1/box.html#outer-edge)不能高于任何含有源文档中在此之前的元素生成的盒的行盒的top
7. 左边存在另一个左浮动盒的左浮动盒的right外边不能位于其包含块的right边的右边（不严谨的：一个左浮动盒不能超出right边，除非它已经尽量向左（紧挨着包含块的left边）了）。右浮动元素也有类似的规则
8. 浮动盒必须尽量高往高放（A floating box must be placed as high as possible）
9. 左浮动盒必须尽量往左放，右浮动盒尽量往右放。更高的位置要比更左/右的位置优先

### 控制浮动后的流：clear

![your text](http://img.hksite.cn/1499871563169)

值不为'none'就隐含了要引入[空隙（clearance）]()。**空隙会阻止margin合并**，并作为元素margin-top上方的空间。用来在竖直方向上推着元素越过浮动（It is used to push the element vertically past the float）

由于空隙是加入在元素的上面，**所以它只能阻止它上面的浮动元素**

#### clearance计算

计算一个设置了'clear'的元素的空隙，先要确定该元素的top border边的假定位置。**如果该元素的top border边的假定位置没有越过（past）相关的浮动（盒），那么就得引入空隙**，并根据8.3.1中的规则**合并margin**

然后，空隙的高度（amount）被设置为下面两者的较大值：

1. 把块的border边放在最低的将被clear的浮动（盒）的bottom外边（outer edge），所必需的高度
2. 把块的top border边放在其假定位置（clear为'none'时的位置），所必需的高度

或者，**空隙被精确地设置为把块的border边放在最低的将被clear的浮动（盒）的bottom外边**，所必需的高度

注意：**空隙可以为负或者为0**

假设有3个块B1，B2，B3，他们的margin分别是M1，M2，M3，他们高度为H1，H2，H3假设B2浮动。

当MB1 = 40px，M2=0，MT3=30px，H2=20px的时候，由于B2浮动，B1和B3由于margin合并，变成4em，所以B2和B3的顶部重合。

![your text](http://img.hksite.cn/1499935733370)

此时，如果加上`clear:left`，那么由于加入了clearance，margin不会合并，所以他们之间的间距变成了30+40=70px，但是H2的高度只有20px，MB1+H2 = 60px < 70px，**所以clearance的高度为-10px**







## 绝对定位

绝对定位模型中，一个盒会有相对于其包含块的**明确偏移**，它会从**常规流**中**全部移除**（不会影响后面的兄弟）。

一个绝对定位的盒会为**常规流中的子级和绝对（不是fixed）定位的后代建立一个新的包含块**。这里明确说明了，后代如果是absolute定位，也在包含块中。

然而一个绝对定位的元素的内容不会沿着任何其它盒排列。它们可能会遮住其它盒的内容（或者它们自身被遮住），取决于重叠盒的[堆叠层级（stack levels）](http://www.ayqy.net/doc/css2-1/visuren.html#stack-level)



用fixed布局，可以实现一个类框架（frame-like）布局





## 'display'，'position'与'float'之间的关系

这3个属性影响盒的生成及布局—['display'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-display)，['position'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-position)和['float'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-float) —间的相互影响如下：

1. 如果['display'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-display)值为'none'，那么['position'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-position)和['float'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-float)不会生效。此时，**元素不生成盒**
2. 否则，如果['position'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-position)值为'absolute'或者'fixed'，盒是绝对定位的，并且['float'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-float)的计算值为'none'。那么display就根据下表来设置，盒的位置将由['top'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-top)，['right'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-right)，['bottom'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-bottom)和['left'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-left)属性和盒的包含块决定
3. 否则，如果'float'值不为'none'，那么盒是浮动的，'display'根据下表来设置
4. 否则，如果该元素是根元素（float=none），'display'根据下表来设置，但指定值'list-item'应该变成计算值'block'还是'list-item'，在CSS 2.1中是未定义的
5. 否则，其它['display'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-display)属性值（计算值）就用指定值

![your text](http://img.hksite.cn/1499870709973)





例子可以看： [绝对定位](http://www.ayqy.net/doc/css2-1/visuren.html#comp-abspos) 例子

主要是行盒的定位

![your text](http://img.hksite.cn/1499948881684)



## z-index 层叠层级

![your text](http://img.hksite.cn/1499948906525)



【在..前面】表示离屏幕和用户更近，其实可以把它想象成xyz三轴，我们看到的屏幕是二维，而第三个维度z轴就是垂直于屏幕的，z值越大，就是越上面，越靠近用户。



每个盒都属于一个**堆叠上下文**（stacking context）。给定堆叠上下文中**每个定位**的（positioned）盒都有一个整型**堆叠层级**（stack level），表示它在同一堆叠上下文中相对于其它堆叠层级的Z轴上的位置。堆叠层级更大的总被格式化在堆叠层级低的前面，**盒可以有负的堆叠层级**。

同一个堆叠上下文中**堆叠层级相同**的盒按照**文档树顺序**从后向前（低向高）堆叠





根元素形成根堆叠上下文。其它堆叠上下文由任意'z-index'的计算值不为'auto'的定位的（positioned）元素（包括相对定位元素）生成，堆叠上下文不一定与包含块有关。在将来的CSS版本中，其它属性可能会引入堆叠上下文，例如，'[opacity](http://www.w3.org/TR/css3-color/#transparency)' [[CSS3COLOR\]](http://www.ayqy.net/doc/css2-1/refs.html#ref-CSS3COLOR).

在每个堆叠上下文中，下列层按从后向前的顺序绘制：

1. 元素的background和border生成的堆叠上下文
2. 堆叠层级为负数的子级堆叠上下文（最负的优先）
3. 流内的，非内联级，未定位的（non-positioned ）后代
4. 未定位的浮动（元素）
5. 流内的，内联级，未定位的后代，包括inline table和inline block
6. 堆叠层级为0的子级堆叠上下文，以及堆叠层级为0的定位的后代
7. 堆叠层级为正数的子级堆叠上下文（最小的优先）

每个堆叠上下文中，都会绘制堆叠层级为0的定位的元素（在第6层），未定位的浮动（元素）（在第4层），inline block（在第5层）和inline table（在第5层），就像这些元素自身生成了新的堆叠上下文一样，除了它们的定位的后代和将要（would-be）加入当前堆叠上下文的子级堆叠上下文





## 宽度

![your text](http://img.hksite.cn/1500037138032)

宽度**初始值为auto**

除了非替换的行内元素和table元素



## margin width 的auto计算

### 1. inline non-replaced

无width属性，为0

height也不适用，跟内容有关（行盒）

一个内联的不可替换盒的竖直padding，border和margin从内容区的top和bottom开始，并且与['line-height'](http://www.ayqy.net/doc/css2-1/visudet.html#propdef-line-height)无关。['line-height'](http://www.ayqy.net/doc/css2-1/visudet.html#propdef-line-height)只用在计算行盒的高度时使用。



### 2. inline replaced

- margin-left和margin-right  auto为0

- 有内在宽度，auto为内在宽度

- 有内在宽高比，有高度，auto就根据宽高比计算，没高度，undefined

- 没有内在宽度，没有高度，没有内在宽高比，auto=300px，如果太宽，用最大比例为2：1矩形代替

- 高度同理，只是最后设置为2:1，高度不超过150px，且宽度不超过最大宽度

  **所有可替换元素高度都参考这个**

 

### 3. 常规流block non-replaced

- margin + border + padding + width  = containing box

  满足这个式子就行，如果有且仅有一个值为auto，那就根据式子计算就好


- 如果width为auto，其他的auto自动变为0。


- 如果margin-left 和margin-right值相等，会让元素水平居中。

  因为margin padding border 默认都是0，所以如果设置了margin: 0 auto，那其实就给margin-left和margin-right设置了相同的值（计算得出）自然就居中了。


- margin-top和bottom为0
- 高度就为**常规流**中的子集撑起来的高度，考虑padding和子盒margin（浮动和相对位移不考虑）

（包括overflow:visible一样适用）

### 4. 常规流中的block replaced

width的值根据2计算，margin根据3计算

高度参考2



### 5. 浮动的non-replaced

margin-left、right 为0

width为自适应（shrink-to-fit) : min(max(首选最小宽度, 可用宽度), 首选宽度)

高度11



### 6. 浮动的replaced

margin-left、right = 0

width参考2

高度2



### 7. 绝对定位non-replaced

**静态位置**（该元素在常规流中的位置）

left+margin+border+padding+width+right

如果left,right,width三个都为auto，那么margin设为0，**width自适应**，left设为静态位置的left边（ltr），计算right（也就**元素不移动**）

如果right,width,left有一个值是auto，可以直接求出

如果有两个值为auto，并且width为auto，那么width**自适应**，然后求另一值

如果right和left为auto，那么left设置为到静态位置的left边（ltr），计算right。



高度：

- 除非指定，否则参考11
- 如果top height bottom都不为auto，那么margin-top和bottom的auto取一样的值。如果margin-top和bottom只有一个auto，并且被过度约束，那么忽略bottom
- 否则margin-top和bottom设为0，top和bottom计算得出（top优先取静态top位置）

### 8. 绝对定位replaced

width根据2求出

如果right,left都为auto，根据direction把left或right设置为静态位置

然后把margin-left和margin-right的auto设为0

说实话没看懂：

> 1. 如果['left'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-left)和['right'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-right)值都是'auto'，然后如果建立静态位置包含块的元素的'direction'属性为'ltr'，把['left'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-left)设置为静态位置，否则如果'direction'为'rtl'，就把['right'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-right)设置为静态位置
> 2. 如果['left'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-left)或者['right'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-right)为'auto'，把所有值为'auto'的['margin-left'](http://www.ayqy.net/doc/css2-1/box.html#propdef-margin-left)或者['margin-right'](http://www.ayqy.net/doc/css2-1/box.html#propdef-margin-right)替换为'0'
> 3. 如果此时['margin-left'](http://www.ayqy.net/doc/css2-1/box.html#propdef-margin-left)和['margin-right'](http://www.ayqy.net/doc/css2-1/box.html#propdef-margin-right)仍然是'auto'，解方程时要添上额外的约束：2个margin必须相等，除非这会让它们为负。如果包含块的direction是'ltr' ('rtl')，把['margin-left'](http://www.ayqy.net/doc/css2-1/box.html#propdef-margin-left) (['margin-right'](http://www.ayqy.net/doc/css2-1/box.html#propdef-margin-right))设置为0，再求出['margin-right'](http://www.ayqy.net/doc/css2-1/box.html#propdef-margin-right) (['margin-left'](http://www.ayqy.net/doc/css2-1/box.html#propdef-margin-left))
> 4. 如果此时只剩一个'auto'了，就解方程求出这个值
> 5. 如果此时该值被过度限制了，忽略['left'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-left)（包含块的['direction'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-direction)属性为'rtl'时）或者['right'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-right)（['direction'](http://www.ayqy.net/doc/css2-1/visuren.html#propdef-direction)为'ltr'时），再求出这个值

高度计算同7，但是高度通过2求出



### 9. 常规流中的inline-block non-replaced

margin 0

**width 自适应**

高度11



### 10. 常规流中的inline-block replaced

和2一样



### 11. 块格式化上下文的高度auto

- 内联子级：最高行盒和最低行盒之间距离
- 块级子级：最高块盒margin-top边的最低块盒margin-bttom边
- **绝对**定位的**子级忽略**
- 子级可以是匿名块
- 高度要能把**浮动子级的底边包括进去**

## max-width min-width

![your text](http://img.hksite.cn/1500093560762)



首先，先不管max-width和min-width计算一遍width，如果出现了违反条件，再做处理。

![your text](http://img.hksite.cn/1500093224095)



## 高度

![your text](http://img.hksite.cn/1500093587224)

高度的初始值为auto

**百分比的设置**：百分比参照生成盒的[包含块](http://www.ayqy.net/doc/css2-1/visuren.html#containing-block)的高度。**如果包含块的高度没有显式指定**（即取决于内容高度），并且该元素不是绝对定位的，**则计算值为'auto'**。[根元素](http://www.ayqy.net/doc/css2-1/conform.html#root)上的百分比高度是相对于[初始包含块](http://www.ayqy.net/doc/css2-1/visudet.html#containing-block-details)的 

注意：对于那些包**含块基于一个块级元素的绝对定位的元素**，百分比根据**这个元素的*padding box*的高度来计算**。这与CSS1不同，（CSS1中）百分比总是根据父级元素的*content box*来计算



## 行高的计算

如[内联格式化上下文](http://www.ayqy.net/doc/css2-1/visuren.html#inline-formatting)节所述，用户代理把内联级盒排列在一个[行盒](http://www.ayqy.net/doc/css2-1/visuren.html#line-box)的垂直堆叠里。行盒的高度由下列规则决定：

1. 计算行盒中每个内联级盒的高度时，对于可替换元素，inline-block元素和inline-table元素，这个值就是其margin box的高度；对于内联盒，这个值是其'line-height'（见[“计算height与margin”](http://www.ayqy.net/doc/css2-1/visudet.html#Computing_heights_and_margins)和[“行距（Leading）与半行距”](http://www.ayqy.net/doc/css2-1/visudet.html#leading)中的[内联盒的高度](http://www.ayqy.net/doc/css2-1/visudet.html#inline-box-height)）
2. 内联级盒是根据其['vertical-align'](http://www.ayqy.net/doc/css2-1/visudet.html#propdef-vertical-align)属性竖直对齐的。如果它们是'top'或者'bottom'对齐，它们必须对齐得让行盒高度最小化。如果这样的盒足够高，存在多个解，而CSS 2.1没有定义行盒基线的位置（即，[strut](http://www.ayqy.net/doc/css2-1/visudet.html#strut)的位置，见下文）
3. 行盒高度是最高的盒的top与最低的盒的bottom之间的距离（包括下面['line-height'](http://www.ayqy.net/doc/css2-1/visudet.html#propdef-line-height)中解释的[strut](http://www.ayqy.net/doc/css2-1/visudet.html#strut)）

空内联元素生成空的内联盒，但这些盒仍然具有margin，padding，border和line height，并因此会影响这些计算，就像有内容的元素一样